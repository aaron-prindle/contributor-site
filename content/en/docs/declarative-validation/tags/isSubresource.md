# `+k8s:isSubresource`

**Description:**

The `+k8s:isSubresource` tag is a package-level comment tag used to specify that the validation rules defined within the package should only be applied when validating a specific subresource.

For this tag to be effective, the subresource it refers to must be declared as supported in the API type's main package using the `+k8s:supportsSubresource` tag. This allows for creating separate packages of validation rules that are conditionally applied based on the subresource being accessed during an API request.

**Scope:** Package

**Payload:**

*   `<subresource-path>`: The path of the subresource to which the validations should apply (e.g., `"/status"`, `"/scale"`).

**Usage Example:**

Imagine you have validations that should only run for the `/scale` subresource of a `Deployment`.

In the main package for the `Deployment` type:
```go
// staging/src/k8s.io/api/apps/v1/doc.go

// +k8s:supportsSubresource="/scale"
package v1
```

In a separate package containing only the scale-specific validations:
```go
// staging/src/k8s.io/code-generator/cmd/validation-gen/output_tests/tags/supported_resources/issubresource/doc.go

// +k8s:isSubresource="/scale"
package issubresource

type T1 struct {
	// This validation only applies to the /scale subresource.
	// +k8s:validateTrue="field T1.S"
	S string `json:"s"`
}
```
