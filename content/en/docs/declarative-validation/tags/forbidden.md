# `+k8s:forbidden`

**Description:**

Indicates that a field may not be specified.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:forbidden
    MyField string `json:"myField"`
}
```

In this example, `MyField` cannot be provided (it is forbidden) when creating or updating `MyObject`.
