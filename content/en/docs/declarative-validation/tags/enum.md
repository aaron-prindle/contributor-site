# `+k8s:enum`

**Description:**

Indicates that a string type is an enum. All const values of this type are considered values in the enum.

**Usage Example:**

First, define a new string type and some constants of that type:

```go
// +k8s:enum
type MyEnum string

const (
    MyEnumA MyEnum = "A"
    MyEnumB MyEnum = "B"
)
```

Then, use this type in another struct:

```go
type MyObject struct {
    MyField MyEnum `json:"myField"`
}
```

The validation logic will ensure that `MyField` is one of the defined enum values (`"A"` or `"B"`).
