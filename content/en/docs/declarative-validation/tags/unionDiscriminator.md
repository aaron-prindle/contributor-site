# `+k8s:unionDiscriminator`

**Description:**

Indicates that this field is the discriminator for a union.

**Arguments:**

*   `union` (string, optional): The name of the union, if more than one exists.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:unionDiscriminator
    Type string `json:"type"`

    // +k8s:unionMember
    A *A `json:"a,omitempty"`

    // +k8s:unionMember
    B *B `json:"b,omitempty"`
}
```

In this example, the `Type` field is the discriminator for the union. The value of `Type` will determine which of the union members (`A` or `B`) is expected to be present.
