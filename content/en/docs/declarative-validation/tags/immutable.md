# `+k8s:immutable`

**Description:**

Indicates that a field may not be updated.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:immutable
    MyField string `json:"myField"`
}
```

In this example, `MyField` cannot be changed after the object is created.
