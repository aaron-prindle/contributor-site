# `+k8s:optional`

**Description:**

Indicates that a field is optional to clients.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:optional
    MyField string `json:"myField"`
}
```

In this example, `MyField` is not required to be provided when creating or updating `MyObject`.
