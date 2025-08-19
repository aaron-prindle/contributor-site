# `+k8s:unionMember`

**Description:**

Indicates that this field is a member of a union.

**Arguments:**

*   `union` (string, optional): The name of the union, if more than one exists.
*   `memberName` (string, optional): The discriminator value for this member. Defaults to the field's name.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:unionDiscriminator
    Type string `json:"type"`

    // +k8s:unionMember
    A *A `json:"a,omitempty"`

    // +k8s:unionMember="b-member"
    B *B `json:"b,omitempty"`
}
```

In this example, `A` and `B` are members of the union. If the `Type` field has the value `"a"`, then the `a` field is expected to be present. If the `Type` field has the value `"b-member"`, then the `b` field is expected to be present.
