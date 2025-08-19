# `+k8s:opaqueType`

**Description:**

Indicates that any validations declared on the referenced type will be ignored. If a referenced type's package is not included in the generator's current flags, this tag must be set, or code generation will fail (preventing silent mistakes). If the validations should not be ignored, add the type's package to the generator using the `--readonly-pkg` flag.

**Usage Example:**

```go
import "some/external/package"

type MyObject struct {
    // +k8s:opaqueType
    ExternalField package.ExternalType `json:"externalField"`
}
```

In this example, any validation tags on `package.ExternalType` will be ignored.
