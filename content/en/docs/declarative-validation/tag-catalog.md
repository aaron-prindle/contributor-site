# Declarative Validation Tag Catalog

Below is a list of all the available Declarative Validation (DV) tags.

| Tag | Description |
| --- | --- |
| [`+k8s:eachKey`](./tags/eachKey.md) | Declares a validation for each key in a map. |
| [`+k8s:eachVal`](./tags/eachVal.md) | Declares a validation for each value in a map or list. |
| [`+k8s:enum`](./tags/enum.md) | Indicates that a string type is an enum. |
| [`+k8s:forbidden`](./tags/forbidden.md) | Indicates that a field may not be specified. |
| [`+k8s:format`](./tags/format.md) | Indicates that a string field has a particular format. |
| [`+k8s:ifDisabled`](./tags/ifDisabled.md) | Declares a validation that only applies when an option is disabled. |
| [`+k8s:ifEnabled`](./tags/ifEnabled.md) | Declares a validation that only applies when an option is enabled. |
| [`+k8s:immutable`](./tags/immutable.md) | Indicates that a field may not be updated. |
| [`+k8s:isSubresource`](./tags/isSubresource.md) | Specifies that validations in a package only apply to a specific subresource. |
| [`+k8s:item`](./tags/item.md) | Declares a validation for an item of a slice declared as a `+k8s:listType=map`. |
| [`+k8s:listMapKey`](./tags/listMapKey.md) | Declares a named sub-field of a list's value-type to be part of the list-map key. |
| [`+k8s:listType`](./tags/listType.md) | Declares a list field's semantic type. |
| [`+k8s:maxItems`](./tags/maxItems.md) | Indicates that a list field has a limit on its size. |
| [`+k8s:maxLength`](./tags/maxLength.md) | Indicates that a string field has a limit on its length. |
| [`+k8s:minimum`](./tags/minimum.md) | Indicates that a numeric field has a minimum value. |
| [`+k8s:neq`](./tags/neq.md) | Verifies the field's value is not equal to a specific disallowed value. |
| [`+k8s:opaqueType`](./tags/opaqueType.md) | Indicates that any validations declared on the referenced type will be ignored. |
| [`+k8s:optional`](./tags/optional.md) | Indicates that a field is optional to clients. |
| [`+k8s:required`](./tags/required.md) | Indicates that a field must be specified by clients. |
| [`+k8s:subfield`](./tags/subfield.md) | Declares a validation for a subfield of a struct. |
| [`+k8s:supportsSubresource`](./tags/supportsSubresource.md) | Declares a supported subresource for the types within a package. |
| [`+k8s:unionDiscriminator`](./tags/unionDiscriminator.md) | Indicates that this field is the discriminator for a union. |
| [`+k8s:unionMember`](./tags/unionMember.md) | Indicates that this field is a member of a union. |
| [`+k8s:zeroOrOneOfMember`](./tags/zeroOrOneOfMember.md) | Indicates that this field is a member of a zero-or-one-of union. |