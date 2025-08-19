# `+k8s:maxLength`

**Description:**

Indicates that a string field has a limit on its length.

**Payload:**

*   `<non-negative integer>`: This field must be no more than X characters long.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:maxLength=10
    MyString string `json:"myString"`
}
```

In this example, `MyString` cannot be longer than 10 characters.
