# `+k8s:required`

**Description:**

Indicates that a field must be specified by clients.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:required
    MyField string `json:"myField"`
}
```

In this example, `MyField` must be provided when creating or updating `MyObject`.
