# `+k8s:format`

**Description:**

Indicates that a string field has a particular format.

**Payloads:**

*   `k8s-ip`: This field holds an IPv4 or IPv6 address value. IPv4 octets may have leading zeros.
*   `k8s-long-name`: This field holds a Kubernetes "long name", aka a "DNS subdomain" value.
*   `k8s-short-name`: This field holds a Kubernetes "short name", aka a "DNS label" value.

**Usage Example:**

```go
type MyObject struct {
    // +k8s:format=k8s-ip
    IPAddress string `json:"ipAddress"`

    // +k8s:format=k8s-long-name
    Subdomain string `json:"subdomain"`

    // +k8s:format=k8s-short-name
    Label string `json:"label"`
}
```

In this example:
*   `IPAddress` must be a valid IP address.
*   `Subdomain` must be a valid DNS subdomain.
*   `Label` must be a valid DNS label.
