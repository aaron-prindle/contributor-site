# `+k8s:eachVal`

**Description:**

Declares a validation for each value in a map or list.

**Payload:**

*   `<validation-tag>`: The tag to evaluate for each value.

**Usage Example:**

**Usage Example:**

```go
type MyObject struct {
    // +k8s:eachVal=+k8s:minimum=1
    MyMap map[string]int `json:"myMap"`
}
```

In this example, `eachVal` is used to specify that the `+k8s:minimum` tag should be applied to each element in `MyList`. This means that all fields in `MyStruct` must be >= 1.
