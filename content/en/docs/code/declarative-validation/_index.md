---
content_type: "reference"
title: Declarative API validation
weight: 50
description: |
  Declarative validation behavior, rollout, feature gates, and validation tags for Kubernetes APIs.
---

Declarative validation lets Kubernetes API authors put common validation rules next to the versioned API types they apply to. Instead of hand-writing every basic check in `validation.go`, API authors add `+k8s:` tags to `types.go`, and `validation-gen` turns those tags into Go validation code.

This does not remove handwritten validation. Cross-field checks, compatibility quirks, and rules that cannot be expressed as tags still live in handwritten code. The goal is to move the simple, repeated, field-local rules into a form that is easier to review and harder to accidentally drift.

## TL;DR

- Put validation tags on versioned API types.
- Run `validation-gen` after adding or changing tags.
- For new APIs that use declarative enforcement from day one, unwrapped tags can be authoritative immediately.
- For migrations of existing handwritten validation, use lifecycle wrappers so the generated result can soak before it becomes authoritative.
- Watch `declarative_validation_mismatch_total` during shadow phases. A mismatch means generated validation and handwritten validation disagree.

## Rollout and feature gates

Declarative validation has two separate concerns:

- whether generated validation code runs at all;
- whether a particular generated error is returned to the user or only compared against handwritten validation.

The current rollout uses these feature gates:

| Feature gate | Stage | Default | What it does |
| --- | --- | --- | --- |
| `DeclarativeValidation` | GA in v1.36 | `true`, locked to default | Runs generated validation where it has been wired in. For shadowed migrated rules, handwritten validation remains authoritative and generated validation is compared against it. |
| `DeclarativeValidationBeta` | Beta | `true` | Controls `+k8s:beta` validation rules. When enabled, Beta rules reject invalid requests. When disabled, Beta rules fall back to shadow mode. |
| `DeclarativeValidationTakeover` | Deprecated in v1.36 | n/a | Previously controlled whether declarative validation was authoritative. It is no longer honored, but may still be accepted to avoid unknown-gate errors. |

Validation rules are staged with lifecycle wrappers:

| Rule shape | Enforcement behavior |
| --- | --- |
| `+k8s:alpha(since: "1.N")=<validator>` | Always shadowed. Handwritten validation wins. Mismatches are reported through metrics. |
| `+k8s:beta(since: "1.N")=<validator>` | Enforced when `DeclarativeValidationBeta=true`; shadowed when the gate is disabled. |
| `<validator>` | Stable/unwrapped. Always enforced. |

For new API work, check the strategy wiring. New APIs that rely on declarative validation as the source of truth need declarative enforcement enabled in their strategy. For migrations, the generated result should usually start shadowed so we can prove it matches the existing handwritten behavior.

## Disabling `DeclarativeValidationBeta` {#opt-out}

Cluster administrators can set `DeclarativeValidationBeta=false` to move `+k8s:beta` rules back to shadow mode. This is mostly a rollback valve.

Reasons to consider disabling it:

- Beta declarative validation rejects requests that should still be valid.
- Beta declarative validation allows objects that handwritten validation would have rejected.
- `declarative_validation_mismatch_total` increases for resources that matter to the cluster.
- API server latency changes line up with declarative validation being enabled.

For feature gate mechanics, see the Kubernetes [feature gates](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/) documentation.

## Downgrade and rollback notes

Disabling `DeclarativeValidationBeta` makes Beta rules shadowed again. That is the normal escape hatch.

The awkward rollback case is a bad declarative rule that allowed an invalid object to be persisted while Beta enforcement was on. After disabling the gate, later updates to that object may be blocked by the still-authoritative handwritten validation. In that case the object has to be corrected before normal updates can proceed.

## Tag catalog {#catalog}

| Tag | What it is for | Stability |
| --- | --- | --- |
| [`+k8s:alpha`](#tag-alpha) | Shadow a validation rule while handwritten validation remains authoritative. | Stable |
| [`+k8s:beta`](#tag-beta) | Enforce a migrated rule by default, with rollback through `DeclarativeValidationBeta`. | Stable |
| [`+k8s:eachKey`](#tag-eachKey) | Apply a validator to every key in a map. | Alpha |
| [`+k8s:eachVal`](#tag-eachVal) | Apply a validator to every value in a map or list. | Alpha |
| [`+k8s:enum`](#tag-enum) | Treat all constants of a string type as the allowed values. | Beta |
| [`+k8s:forbidden`](#tag-forbidden) | Reject a field when it is specified. | Alpha |
| [`+k8s:format`](#tag-format) | Validate a string against a Kubernetes-defined format. | Stable |
| [`+k8s:ifDisabled`](#tag-ifDisabled) | Run a nested validator only when an option is disabled. | Alpha |
| [`+k8s:ifEnabled`](#tag-ifEnabled) | Run a nested validator only when an option is enabled. | Alpha |
| [`+k8s:isSubresource`](#tag-isSubresource) | Mark a package as validation for a specific subresource. | Stable |
| [`+k8s:item`](#tag-item) | Apply a validator to one identified item in a list-map. | Stable |
| [`+k8s:listMapKey`](#tag-listMapKey) | Name the key field for a list-map. | Stable |
| [`+k8s:listType`](#tag-listType) | Declare list semantics: `atomic`, `map`, or `set`. | Stable |
| [`+k8s:maxItems`](#tag-maxItems) | Limit the number of items in a list. | Stable |
| [`+k8s:maxLength`](#tag-maxLength) | Limit string length. | Stable |
| [`+k8s:minimum`](#tag-minimum) | Require a numeric minimum. | Stable |
| [`+k8s:neq`](#tag-neq) | Reject one specific value. | Alpha |
| [`+k8s:opaqueType`](#tag-opaqueType) | Ignore validations declared on a referenced type. | Alpha |
| [`+k8s:optional`](#tag-optional) | Mark a field as optional to clients. | Stable |
| [`+k8s:required`](#tag-required) | Mark a field as required from clients. | Stable |
| [`+k8s:subfield`](#tag-subfield) | Apply a validator to a direct subfield. | Stable |
| [`+k8s:supportsSubresource`](#tag-supportsSubresource) | Declare supported validation subresources for a package. | Stable |
| [`+k8s:unionDiscriminator`](#tag-unionDiscriminator) | Mark the discriminator field for a union. | Stable |
| [`+k8s:unionMember`](#tag-unionMember) | Mark a field as a union member. | Stable |
| [`+k8s:zeroOrOneOfMember`](#tag-zeroOrOneOfMember) | Mark a field as part of an at-most-one group. | Stable |

## Tag reference

### `+k8s:alpha` {#tag-alpha}

Use this wrapper when a migrated validation rule should run in shadow mode. The handwritten result is still what users see; generated validation is compared against it.

- Argument: `since`, required, formatted as a Kubernetes minor version string.
- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:alpha(since: "1.36")=+k8s:minimum=1
    MyField int `json:"myField"`
}
```

Do not use this to try to disable handwritten validation. It only controls the lifecycle stage of the generated rule.

### `+k8s:beta` {#tag-beta}

Use this wrapper after an Alpha rule has soaked cleanly, or for migrations that are allowed to start at Beta. With `DeclarativeValidationBeta=true`, the generated rule is authoritative. With the gate disabled, it falls back to shadow mode.

- Argument: `since`, required, formatted as a Kubernetes minor version string.
- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:beta(since: "1.37")=+k8s:minimum=1
    MyField int `json:"myField"`
}
```

When graduating a rule, update `since:` to the version where the rule entered the new stage.

### `+k8s:eachKey` {#tag-eachKey}

Applies a nested validator to each key in a map.

- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:eachKey=+k8s:minimum=1
    MyMap map[int]string `json:"myMap"`
}
```

In this case every key in `MyMap` must be at least `1`.

### `+k8s:eachVal` {#tag-eachVal}

Applies a nested validator to each value in a map or list.

- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:eachVal=+k8s:minimum=1
    MyMap map[string]int `json:"myMap"`
}
```

In this case every value in `MyMap` must be at least `1`.

### `+k8s:enum` {#tag-enum}

Marks a string type as an enum. All constants of that type become the allowed values.

```go
// +k8s:enum
type MyEnum string

const (
    MyEnumA MyEnum = "A"
    MyEnumB MyEnum = "B"
)

type MyStruct struct {
    MyField MyEnum `json:"myField"`
}
```

Generated validation rejects values outside the declared constants.

### `+k8s:forbidden` {#tag-forbidden}

Rejects the field when it is specified.

```go
type MyStruct struct {
    // +k8s:forbidden
    MyField string `json:"myField"`
}
```

Use this when a field exists in a shape but must not be set in a particular context.

### `+k8s:format` {#tag-format}

Validates a string against a named Kubernetes format.

Supported payloads in this migrated reference:

- `k8s-ip`: IPv4 or IPv6 address.
- `k8s-long-name`: Kubernetes long name / DNS subdomain.
- `k8s-short-name`: Kubernetes short name / DNS label.

```go
type MyStruct struct {
    // +k8s:format=k8s-ip
    IPAddress string `json:"ipAddress"`

    // +k8s:format=k8s-long-name
    Subdomain string `json:"subdomain"`

    // +k8s:format=k8s-short-name
    Label string `json:"label"`
}
```

### `+k8s:ifDisabled` {#tag-ifDisabled}

Runs a nested validator only when the named validation option is disabled.

- Argument: option name.
- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:ifDisabled("my-feature")=+k8s:required
    MyField string `json:"myField"`
}
```

### `+k8s:ifEnabled` {#tag-ifEnabled}

Runs a nested validator only when the named validation option is enabled.

- Argument: option name.
- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:ifEnabled("my-feature")=+k8s:required
    MyField string `json:"myField"`
}
```

### `+k8s:isSubresource` {#tag-isSubresource}

Marks a package as the validation implementation for a specific subresource. This is package-level metadata.

It depends on `+k8s:supportsSubresource` in the main API package. Without that matching support declaration, generated subresource validation code may exist but not be reachable through the dispatcher.

- Payload: subresource path, such as `"/status"` or `"/scale"`.

Main API package:

```go
// +k8s:supportsSubresource="/scale"
package v1
```

Subresource validation package:

```go
// +k8s:isSubresource="/scale"
package scale
```

Use this when subresource validation needs to live separately from root-object validation.

### `+k8s:item` {#tag-item}

Applies a nested validator to one item in a list-map. The item is selected by its list-map key fields.

- Arguments: JSON field/value pairs for all `listMapKey` fields.
- Payload: one validation tag.

```go
type MyStruct struct {
    // +k8s:listType=map
    // +k8s:listMapKey=type
    // +k8s:item(type: "Approved")=+k8s:zeroOrOneOfMember
    // +k8s:item(type: "Denied")=+k8s:zeroOrOneOfMember
    MyConditions []MyCondition `json:"conditions"`
}

type MyCondition struct {
    Type   string `json:"type"`
    Status string `json:"status"`
}
```

Here only the items with `type: "Approved"` or `type: "Denied"` get the nested validation.

### `+k8s:listMapKey` {#tag-listMapKey}

Names a field that participates in the key for a list-map. Use this with `+k8s:listType=map`.

- Payload: JSON field name.
- May be repeated for compound keys.

```go
// +k8s:listType=map
// +k8s:listMapKey=keyFieldOne
// +k8s:listMapKey=keyFieldTwo
type MyList []MyStruct

type MyStruct struct {
    keyFieldOne string `json:"keyFieldOne"`
    keyFieldTwo string `json:"keyFieldTwo"`
    valueField   string `json:"valueField"`
}
```

### `+k8s:listType` {#tag-listType}

Declares list semantics.

- `atomic`: treat the list as one value.
- `map`: treat the list as a keyed map. Requires `+k8s:listMapKey`.
- `set`: treat the list as a set of unique values.

```go
// +k8s:listType=map
// +k8s:listMapKey=keyField
type MyList []MyStruct

type MyStruct struct {
    keyField  string `json:"keyField"`
    valueField string `json:"valueField"`
}
```

List semantics matter for validation and for old/new correlation during updates.

### `+k8s:maxItems` {#tag-maxItems}

Limits the number of items in a list.

- Payload: non-negative integer.

```go
type MyStruct struct {
    // +k8s:maxItems=5
    MyList []string `json:"myList"`
}
```

### `+k8s:maxLength` {#tag-maxLength}

Limits string length.

- Payload: non-negative integer.

```go
type MyStruct struct {
    // +k8s:maxLength=10
    MyString string `json:"myString"`
}
```

### `+k8s:minimum` {#tag-minimum}

Requires a numeric value to be greater than or equal to the payload.

- Payload: integer.

```go
type MyStruct struct {
    // +k8s:minimum=0
    MyInt int `json:"myInt"`
}
```

### `+k8s:neq` {#tag-neq}

Rejects one specific value.

- Payload: string, integer, or boolean value.

```go
type MyStruct struct {
    // +k8s:neq="disallowed"
    MyString string `json:"myString"`

    // +k8s:neq=0
    MyInt int `json:"myInt"`

    // +k8s:neq=true
    MyBool bool `json:"myBool"`
}
```

### `+k8s:opaqueType` {#tag-opaqueType}

Tells `validation-gen` to ignore validations declared on the referenced type.

Use this when the referenced package is outside the generator inputs and you intentionally do not want to pull its validation tags into the current generated output.

```go
import "some/external/package"

type MyStruct struct {
    // +k8s:opaqueType
    ExternalField package.ExternalType `json:"externalField"`
}
```

If you do want validations from that referenced type, add the package to the generator with `--readonly-pkg` instead.

### `+k8s:optional` {#tag-optional}

Marks a field as optional to clients.

```go
type MyStruct struct {
    // +k8s:optional
    MyField string `json:"myField"`
}
```

### `+k8s:required` {#tag-required}

Marks a field as required from clients.

```go
type MyStruct struct {
    // +k8s:required
    MyField string `json:"myField"`
}
```

### `+k8s:subfield` {#tag-subfield}

Applies a nested validator to a direct subfield of a struct, or to a field promoted through an embedded struct.

- Argument: JSON name of the subfield.
- Payload: one validation tag.

```go
type Wrapper struct {
    // +k8s:subfield("name")=+k8s:required
    Metadata ObjectMeta `json:"metadata"`
}

type ObjectMeta struct {
    Name string `json:"name"`
}
```

### `+k8s:supportsSubresource` {#tag-supportsSubresource}

Declares which subresources the validation dispatcher should recognize for the types in a package. This is package-level metadata.

- Payload: subresource path, such as `"/status"` or `"/scale"`.
- May be repeated for multiple subresources.

```go
// +k8s:supportsSubresource="/status"
// +k8s:supportsSubresource="/scale"
package v1
```

If a package has no `+k8s:supportsSubresource` tags, generated validation is only wired for the root resource.

### `+k8s:unionDiscriminator` {#tag-unionDiscriminator}

Marks the discriminator field for a union.

- Optional argument: `union`, used when a struct has more than one union.

```go
type MyStruct struct {
    // +k8s:unionDiscriminator
    Type MyType `json:"type"`

    // +k8s:unionMember
    // +k8s:optional
    OptionA *OptionA `json:"optionA"`

    // +k8s:unionMember
    // +k8s:optional
    OptionB *OptionB `json:"optionB"`
}
```

The discriminator value determines which member is expected.

### `+k8s:unionMember` {#tag-unionMember}

Marks a field as a member of a union.

- Optional argument: `union`, used when a struct has more than one union.
- Optional argument: `memberName`, used when the discriminator value differs from the field name.

```go
type MyStruct struct {
    // +k8s:unionMember(union: "backend", memberName: "service")
    // +k8s:optional
    Service *ServiceBackend `json:"service"`

    // +k8s:unionMember(union: "backend", memberName: "resource")
    // +k8s:optional
    Resource *ResourceBackend `json:"resource"`
}
```

### `+k8s:zeroOrOneOfMember` {#tag-zeroOrOneOfMember}

Marks a field as part of a group where at most one member may be set. It is valid for none of the members to be set.

- Optional argument: `union`, used when a struct has more than one group.
- Optional argument: `memberName`, used when the member name differs from the field name.

```go
type MyStruct struct {
    // +k8s:zeroOrOneOfMember
    // +k8s:optional
    Foo *Foo `json:"foo"`

    // +k8s:zeroOrOneOfMember
    // +k8s:optional
    Bar *Bar `json:"bar"`
}
```

## Review checklist

Use this as a quick pass when reviewing a PR that adds declarative validation:

- Are the tags on the versioned API types?
- Did the PR regenerate `zz_generated.validations.go`?
- For migrations, does handwritten validation still match the generated result?
- For lifecycle wrappers, is `since:` set to the version where the rule entered that stage?
- For subresources, do `+k8s:supportsSubresource` and `+k8s:isSubresource` line up?
