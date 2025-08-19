# `+k8s:zeroOrOneOfMember`

**Description:**

Indicates that this field is a member of a zero-or-one-of union. A zero-or-one-of union allows at most one member to be set. Unlike regular unions, having no members set is valid.

**Arguments:**

*   `union` (string, optional): The name of the union, if more than one exists.
*   `memberName` (string, optional): The custom member name for this member. Defaults to the field's name.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:zeroOrOneOfMember
    A *A `json:"a,omitempty"`

    // +k8s:zeroOrOneOfMember
    B *B `json:"b,omitempty"`
}
```

In this example, at most one of `A` or `B` can be set. It is also valid for neither to be set.
