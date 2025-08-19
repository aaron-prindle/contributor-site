# `+k8s:ifDisabled`

**Description:**

Declares a validation that only applies when an option is disabled.

**Arguments:**

*   `<option>` (string, required): The name of the option.

**Payload:**

*   `<validation-tag>`: This validation tag will be evaluated only if the validation option is disabled.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:ifDisabled("my-feature")=+k8s:required
    MyField string `json:"myField"`
}
```

In this example, `MyField` is required only if the "my-feature" option is disabled.
