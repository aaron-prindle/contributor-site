# `+k8s:listType`

**Description:**

Declares a list field's semantic type. This tag is used to specify how a list should be treated, for example, as a map or a set.

**Payload:**

*   `atomic`: The list is treated as a single atomic value.
*   `map`: The list is treated as a map, where each element has a unique key. Requires the use of `+k8s:listMapKey`.
*   `set`: The list is treated as a set, where each element is unique.

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

In this example, `MyList` is declared as a list of type `map`, with `keyField` as the key. This means that the validation logic will ensure that each element in the list has a unique `keyField`.
