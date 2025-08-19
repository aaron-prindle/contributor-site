# `+k8s:eachKey`

**Description:**

Declares a validation for each key in a map.

**Payload:**

*   `<validation-tag>`: The tag to evaluate for each key.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:eachKey=+k8s:minimum=1
    MyMap map[int]string `json:"myMap"`
}
```

In this example, `eachKey` is used to specify that the `+k8s:minimum` tag should be applied to each `int` key in `MyMap`. This means that all keys in the map must be >= 1.
