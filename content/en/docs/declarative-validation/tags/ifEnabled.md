# `+k8s:ifEnabled`

**Description:**

Declares a validation that only applies when an option is enabled.

**Arguments:**

*   `<option>` (string, required): The name of the option.

**Payload:**

*   `<validation-tag>`: This validation tag will be evaluated only if the validation option is enabled.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:ifEnabled("my-feature")=+k8s:required
    MyField string `json:"myField"`
}
```

In this example, `MyField` is required only if the "my-feature" option is enabled.
