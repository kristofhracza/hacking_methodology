# Basic Information
**QUIC (Quick UDP Internet Connections)** is a modern transport layer network protocol developed by Google, designed to provide faster and more secure internet connections. 
Operating over UDP port 443, QUIC integrates features such as multiplexed streams, built-in encryption equivalent to TLS 1.3, and reduced connection latency, making it well-suited for web applications and **HTTP/3**.


# Access Pages with Curl

```bash
curl --http3 https://site.com
```

# Build From Source

Refer to **[this](https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version)** if your version of `curl` doesn't support QUIC.

# References
- [Explanation and guide](https://www.debugbear.com/blog/http3-quic-protocol-guide)
- [Wikipedia](https://en.wikipedia.org/wiki/QUIC)
