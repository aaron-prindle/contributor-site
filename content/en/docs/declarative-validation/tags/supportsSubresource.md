# `+k8s:supportsSubresource`

**Description:**

The `+k8s:supportsSubresource` tag is a package-level comment tag that declares a supported subresource for the types within that package. This tag informs the validation generator that the specified subresource is a valid context for applying validations. Multiple tags can be used to declare support for several subresources.

This tag does not, by itself, apply any validation rules. It only signals which subresources are available for validation checks defined by other tags like `+k8s:isSubresource`.

**Scope:** Package

**Payload:**

*   `<subresource-path>`: The path of the subresource to support (e.g., `"/status"`, `"/scale"`).

**Usage Example:**

```go
// staging/src/k8s.io/api/core/v1/doc.go

// +k8s:supportsSubresource="/status"
// +k8s:supportsSubresource="/scale"
package v1
```

In this example, the package `v1` declares that the `/status` and `/scale` subresources are supported, allowing validation rules to be specifically targeted to them.
