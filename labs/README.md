# Hands-on Networking Labs

[Home](../README.md) · [Handbook](../docs/README.md) · [Roadmap](../ROADMAP.md)

These labs turn concepts into observable behavior. Run them only on systems and networks you own or are authorized to test.

| Lab | Skills | Status |
|---|---|:---:|
| [01 — Observe basic connectivity](01-basic-connectivity/README.md) | Interfaces, routes, ping, packet capture | ✅ |
| [02 — Observe the TCP/IP stack](02-observe-tcp-ip-stack/README.md) | DNS, routes, TCP/TLS, layered capture | ✅ |
| [03 — Inspect encapsulation](03-inspect-encapsulation/README.md) | Headers, payloads, MTU, packet bytes | ✅ |
| [04 — Audit local IP configuration](04-audit-ip-configuration/README.md) | Addresses, scope, prefix, routes, source selection | ✅ |
| [05 — Design a VLSM address plan](05-vlsm-address-plan/README.md) | Capacity, VLSM, gateways, validation | ✅ |
| [06 — Inspect Layer 2 identity](06-inspect-layer2-identity/README.md) | MACs, neighbors, frames, gateway identity | ✅ |
| [07 — Observe ARP resolution](07-observe-arp/README.md) | Requests, replies, cache states, gratuitous ARP | ✅ |
| [08 — Diagnose with ICMP](08-diagnose-with-icmp/README.md) | Echo, TTL, unreachable errors, path MTU | ✅ |
| [09 — Compare TCP and UDP](09-compare-tcp-udp/README.md) | Listeners, handshakes, datagrams, captures | ✅ |
| [10 — Trace DNS resolution](10-trace-dns/README.md) | Resolver path, delegation, caching, packet fields | ✅ |
| 11 — Observe DHCP | DORA, leases, options, renewal | 🧭 |
| 12 — Inspect NAT | Translation state and packet tuples | 🧭 |
| 13 — Compare HTTP and HTTPS | Application data and encryption boundaries | 🧭 |
| 14 — Linux network namespace | Isolated interfaces and routes | 🧭 |
| 15 — Docker networking | Bridge, port publishing, container DNS | 🧭 |
| 16 — Kubernetes networking | Pod, Service, DNS, policy | 🧭 |
| 17 — AWS VPC path | Subnets, routes, gateways, security controls | 🧭 |

## Lab standard

Every completed lab includes objectives, prerequisites, topology, commands, expected observations, verification, troubleshooting, cleanup, and reflection questions. Outputs differ by operating system and environment; compare meaning rather than copying addresses literally.
