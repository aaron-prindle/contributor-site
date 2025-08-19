# `+k8s:neq`

**Description:**

Verifies the field's value is not equal to a specific disallowed value. Supports string, integer, and boolean types.

**Payload:**

*   `<value>`: The disallowed value. The parser will infer the type (string, int, bool).

**Usage Example:**

```go
type MyObject struct {
    // +k8s:neq="default"
    MyString string `json:"myString"`

    // +k8s:neq=0
    MyInt int `json:"myInt"`

    // +k8s:neq=true
    MyBool bool `json:"myBool"`
}
```

In this example:
*   `MyString` cannot be equal to `"default"`.
*   `MyInt` cannot be equal to `0`.
*   `MyBool` cannot be equal to `true`.
