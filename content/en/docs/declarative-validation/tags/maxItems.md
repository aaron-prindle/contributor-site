# `+k8s:maxItems`

**Description:**

Indicates that a list field has a limit on its size.

**Payload:**

*   `<non-negative integer>`: This field must be no more than X items long.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:maxItems=5
    MyList []string `json:"myList"`
}
```

In this example, `MyList` cannot contain more than 5 items.
