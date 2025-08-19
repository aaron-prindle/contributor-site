---
title: "How it Works"
---

# How it Works

This document provides a general overview of how the Declarative Validation (DV) tooling functions, from code generation to runtime enforcement.

## Code Generation

The primary tool for DV is `validation-gen`. This tool is responsible for generating the validation logic from the comment tags in your Go source files.

To run the code generator, use the following command:

```bash
hack/update-codegen.sh validation
```

This will generate `zz_generated.validations.go` files in the same directories as your `types.go` files. These generated files contain the validation functions that will be called by the Kubernetes API server.

### The Generation Process

The `validation-gen` tool parses the `+k8s:` comment tags in your Go type definitions. For each tag, it generates a corresponding validation function. For example, the `+k8s:minimum=0` tag will generate a function that checks if the field's value is greater than or equal to 0. [3]

These generated functions are highly optimized Go code, designed for performance within the API server. [3]

## Runtime Validation

The generated validation functions are then registered with the Kubernetes API server. When a resource is created or updated, the API server will call these functions to validate the resource's fields.

During a gradual rollout, the Kubernetes API server can run both the new declarative validation and the old hand-written validation for migrated types and fields. The results are compared internally to ensure consistency. A feature gate, `DeclarativeValidationTakeover`, determines which validation result is authoritative. [1]
