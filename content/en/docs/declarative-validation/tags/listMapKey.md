# `+k8s:listMapKey`

**Description:**

Declares a named sub-field of a list's value-type to be part of the list-map key. This tag is required when `+k8s:listType=map` is used.

**Payload:**

*   `<field-json-name>`: The JSON name of the field to be used as the key.

**Usage Example:**

```go
// +k8s:listType=map
// +k8s:listMapKey=keyField
type MyList []MyStruct

type MyStruct struct {
    // +k8s:validation:Required
    keyField string `json:"keyField"`
    valueField string `json:"valueField"`
}
```

In this example, `listMapKey` is used to specify that the `keyField` of `MyStruct` should be used as the key for the list-map.
