# `+k8s:item`

**Description:**

Declares a validation for an item of a slice declared as a `+k8s:listType=map`. The item to match is declared by providing field-value pair arguments. All key fields must be specified.

**Usage:**

`+k8s:item(stringKey: "value", intKey: 42, boolKey: true)=<validation-tag>`

Arguments must be named with the JSON names of the list-map key fields. Values can be strings, integers, or booleans.

**Payload:**

*   `<validation-tag>`: The tag to evaluate for the matching list item.

**Usage Example:**

```go
// +k8s:listType=map
// +k8s:listMapKey=name
// +k8s:listMapKey=port
// +k8s:item(name: "http", port: 80)=+k8s:immutable
// +k8s:item(name: "https", port: 443)=+k8s:required
type MyList []MyStruct

type MyStruct struct {
    name string `json:"name"`
    port int `json:"port"`
}
```

In this example:
*   The item with `name` "http" and `port` 80 is immutable.
*   The item with `name` "https" and `port` 443 is required.
