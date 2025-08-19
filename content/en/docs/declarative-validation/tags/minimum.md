# `+k8s:minimum`

**Description:**

Indicates that a numeric field has a minimum value.

**Payload:**

*   `<integer>`: This field must be greater than or equal to x.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:minimum=0
    MyInt int `json:"myInt"`
}
```

In this example, `MyInt` must be greater than or equal to 0.
