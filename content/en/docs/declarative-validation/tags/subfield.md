# `+k8s:subfield`

**Description:**

Declares a validation for a subfield of a struct.

**Arguments:**

*   `<field-json-name>` (string, required): The JSON name of the subfield.

**Payload:**

*   `<validation-tag>`: The tag to evaluate for the subfield.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:subfield("mySubfield")=+k8s:required
    MyStruct MyStruct `json:"myStruct"`
}

type MyStruct struct {
    MySubfield string `json:"mySubfield"`
}
```

In this example, `MySubfield` within `MyStruct` is required.
