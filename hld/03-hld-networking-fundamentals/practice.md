# Networking Fundamentals: Practice Questions and Answers

Use these questions to check understanding and practice explaining network behavior in plain language. For design questions, state assumptions before choosing a solution.

## 1) What does the OSI model help you do?

**Answer:** It provides a shared way to group networking responsibilities and narrow down failures. For example, a DNS resolution failure is different from a TCP connection refusal or an HTTP 500 response. The layers are a diagnostic model, not a strict description of every modern protocol implementation.

## 2) What is the difference between an IP address and a port?

**Answer:** An IP address identifies a network interface or destination for routing. A port identifies a process or service endpoint on that host. Together, the address and port identify where transport traffic should be delivered.

## 3) What does the `/24` in an IPv4 CIDR range mean?

**Answer:** It means the first 24 bits form the network prefix. Routers use that prefix to determine which network a destination belongs to. The remaining bits identify addresses within that prefix.

## 4) When would you choose TCP over UDP?

**Answer:** Choose TCP when the application benefits from reliable, ordered byte delivery and standard flow and congestion control, as with many APIs and database connections. UDP can fit latency-sensitive or message-oriented use cases where the application can tolerate loss or implement its own recovery. The choice depends on the protocol and workload, not just a blanket claim that one is faster.

## 5) What does HTTPS protect, and what does it not protect?

**Answer:** HTTPS uses TLS to authenticate the server (when the certificate is correctly validated) and protect data in transit from eavesdropping and undetected modification between TLS endpoints. It does not automatically authenticate the end user, authorize actions, protect data at rest, or encrypt later hops after TLS is terminated.

## 6) Walk through a DNS lookup.

**Answer:** The client asks a recursive resolver for a name. If the answer is not cached, the resolver follows the DNS hierarchy to an authoritative server, receives a record and its TTL, caches it, and returns it. The client then separately connects to the address; a DNS answer does not guarantee the destination is reachable.

## 7) Why can changing a DNS record fail to move traffic immediately?

**Answer:** Recursive resolvers and clients may cache prior answers until their TTL expires, and some systems may cache longer than expected. DNS changes therefore are not instantaneous failover. Use health-aware routing where appropriate and design clients and services to handle stale addresses.

## 8) What does a TLS certificate establish?

**Answer:** A valid certificate helps bind a public key to a hostname through a trusted certificate chain. The client must validate the chain, hostname, and certificate validity. TLS then uses negotiated keys to protect data in transit. A certificate alone does not mean that the application or its operator is trustworthy.

## 9) Are checksums a security mechanism?

**Answer:** A basic checksum or CRC detects accidental corruption but is not resistant to intentional modification. Use a cryptographic hash for tamper detection in an appropriate protocol, and a MAC or digital signature when authenticity is required. TLS already provides authenticated integrity for data within its protected connection.

## 10) How is a reverse proxy different from a forward proxy?

**Answer:** A forward proxy represents clients making outbound requests, often for egress policy or caching. A reverse proxy represents backend services to incoming clients and can route traffic, terminate TLS, balance load, or apply request policy.

## 11) Does NAT provide a firewall?

**Answer:** No. NAT rewrites addresses or ports; it does not define a complete security policy. A firewall explicitly allows or denies traffic. NAT may make unsolicited inbound connections harder in common configurations, but it should not replace firewall rules or service authorization.

## 12) What is the main difference between HTTP/2 and HTTP/3?

**Answer:** HTTP/2 multiplexes streams over TCP, so TCP packet loss can delay delivery across streams sharing that connection. HTTP/3 uses QUIC over UDP and provides independent stream delivery, reducing that cross-stream blocking. Both versions improve aspects of transport compared with HTTP/1.1, but real gains depend on the network and workload.

## 13) Why use persistent connections?

**Answer:** Reusing connections avoids repeated transport setup and often TLS handshakes, reducing latency and CPU. Persistent connections consume resources, so pools, idle timeouts, and maximum connection counts must be tuned to avoid exhausting servers or overwhelming dependencies.

## 14) What are security headers, and are they sufficient on their own?

**Answer:** Security headers tell browsers how to handle content, transport, caching, and referrer information. Examples include HSTS and CSP. They are defense in depth and do not replace safe application code, authentication, authorization, CSRF defenses, or secure cookie settings.

## 15) Does CORS stop a malicious website from sending requests to an API?

**Answer:** Not necessarily. CORS primarily controls whether browser JavaScript may read a cross-origin response. A browser may send some cross-origin requests even when it later blocks the response from JavaScript. CORS is not authentication or a substitute for CSRF defenses.

## 16) What happens during a CORS preflight?

**Answer:** For certain cross-origin requests, the browser sends an `OPTIONS` request describing the intended method and headers. The server responds with the allowed origin, methods, and headers. If the browser accepts that policy, it sends the actual request.

## 17) A URL fails to load. What order would you use to investigate it?

**Answer:** Check name resolution, whether the destination address is correct, whether the route and port are reachable, whether the TCP or QUIC connection succeeds, whether TLS negotiation and certificate validation succeed, and then inspect the HTTP status and application logs. This separates network-layer failures from application failures.

## Quick revision checklist

- Explain the journey from DNS lookup to HTTP response.
- Distinguish IP addresses, ports, and network prefixes.
- Compare TCP and UDP based on guarantees and workload.
- Describe what TLS protects and where its protection ends.
- Explain DNS caching and the limits of DNS-based failover.
- Distinguish a proxy, NAT, and a firewall.
- Compare HTTP/1.1, HTTP/2, and HTTP/3 transport behavior.
- Explain how CORS differs from authentication and CSRF protection.
