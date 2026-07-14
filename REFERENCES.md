# References and Further Reading

[Home](README.md) · [Handbook](docs/README.md) · [Cheatsheets](docs/24-Cheatsheets/README.md)

This handbook is an original educational synthesis. Technical behavior is checked primarily against standards and official documentation; books are used for learning structure, diagrams, and deeper explanation.

## How to use these references

| Source type | Use |
|---|---|
| RFC / IEEE standard | Protocol requirements, field behavior, terminology |
| Official product documentation | Current commands, platform behavior, supported configuration |
| Textbook | Guided learning, examples, exercises, mental models |
| Packet capture / lab | Verify behavior in a controlled environment |

> Not every RFC is an Internet Standard, and standards can be updated or obsoleted. Check each RFC's status and “updated by/obsoletes” metadata in the RFC Editor before using it for production decisions.

## Core standards by handbook chapter

| Chapters | Primary references |
|---|---|
| 00–03 Basics, OSI, TCP/IP, Encapsulation | [RFC 1122 — Internet host requirements](https://www.rfc-editor.org/rfc/rfc1122), [RFC 1123](https://www.rfc-editor.org/rfc/rfc1123), ISO/IEC 7498-1 OSI reference model |
| 04–05 IPv4 and Subnetting | [RFC 791 — IPv4](https://www.rfc-editor.org/rfc/rfc791), [RFC 4632 — CIDR](https://www.rfc-editor.org/rfc/rfc4632), [RFC 1918 — Private IPv4](https://www.rfc-editor.org/rfc/rfc1918), [RFC 3021 — /31 links](https://www.rfc-editor.org/rfc/rfc3021), [RFC 5737 — Documentation ranges](https://www.rfc-editor.org/rfc/rfc5737) |
| 06 MAC / Ethernet | IEEE 802.3 Ethernet and IEEE 802 addressing standards; [IEEE 802 standards catalog](https://standards.ieee.org/standard/802_3-2022.html) |
| 07 ARP | [RFC 826 — Address Resolution Protocol](https://www.rfc-editor.org/rfc/rfc826), [RFC 5227 — IPv4 conflict detection](https://www.rfc-editor.org/rfc/rfc5227) |
| 08 ICMP | [RFC 792 — ICMPv4](https://www.rfc-editor.org/rfc/rfc792), [RFC 1122](https://www.rfc-editor.org/rfc/rfc1122), [RFC 1191 — Path MTU Discovery](https://www.rfc-editor.org/rfc/rfc1191) |
| 09 TCP | [RFC 9293 — TCP](https://www.rfc-editor.org/rfc/rfc9293), [RFC 5681 — Congestion control](https://www.rfc-editor.org/rfc/rfc5681), [RFC 7323 — TCP high-performance extensions](https://www.rfc-editor.org/rfc/rfc7323) |
| 10 UDP | [RFC 768 — UDP](https://www.rfc-editor.org/rfc/rfc768), [RFC 8085 — UDP usage guidelines](https://www.rfc-editor.org/rfc/rfc8085) |
| 11 Ports | [IANA Service Name and Port Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml), [RFC 6335](https://www.rfc-editor.org/rfc/rfc6335) |
| 12 DNS | [RFC 1034 — DNS concepts](https://www.rfc-editor.org/rfc/rfc1034), [RFC 1035 — DNS implementation](https://www.rfc-editor.org/rfc/rfc1035), [RFC 2308 — Negative caching](https://www.rfc-editor.org/rfc/rfc2308), [RFC 6891 — EDNS](https://www.rfc-editor.org/rfc/rfc6891) |
| 13 DHCP | [RFC 2131 — DHCPv4](https://www.rfc-editor.org/rfc/rfc2131), [RFC 2132 — DHCP options](https://www.rfc-editor.org/rfc/rfc2132), [RFC 8415 — DHCPv6](https://www.rfc-editor.org/rfc/rfc8415) |
| 14 NAT | [RFC 3022 — Traditional NAT](https://www.rfc-editor.org/rfc/rfc3022), [RFC 4787 — UDP NAT behavior](https://www.rfc-editor.org/rfc/rfc4787), [RFC 6888 — Carrier-grade NAT](https://www.rfc-editor.org/rfc/rfc6888) |
| 15 VLANs | IEEE 802.1Q Bridges and Bridged Networks; [IEEE 802.1Q catalog](https://standards.ieee.org/standard/802_1Q-2022.html) |
| 16 Switching / STP / LACP | IEEE 802.1Q bridging/STP and IEEE 802.1AX link aggregation; [IEEE 802.1AX catalog](https://standards.ieee.org/standard/802_1AX-2020.html) |
| 17 Routing | [RFC 2328 — OSPFv2](https://www.rfc-editor.org/rfc/rfc2328), [RFC 4271 — BGP-4](https://www.rfc-editor.org/rfc/rfc4271), [RFC 1812 — Router requirements](https://www.rfc-editor.org/rfc/rfc1812) |
| 18 IPv6 | [RFC 8200 — IPv6](https://www.rfc-editor.org/rfc/rfc8200), [RFC 4291 — Addressing architecture](https://www.rfc-editor.org/rfc/rfc4291), [RFC 4861 — Neighbor Discovery](https://www.rfc-editor.org/rfc/rfc4861), [RFC 4862 — SLAAC](https://www.rfc-editor.org/rfc/rfc4862), [RFC 4443 — ICMPv6](https://www.rfc-editor.org/rfc/rfc4443), [RFC 8201 — IPv6 PMTUD](https://www.rfc-editor.org/rfc/rfc8201) |
| 19 Wireshark | [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/), [Display Filter Reference](https://www.wireshark.org/docs/dfref/), [TShark manual](https://www.wireshark.org/docs/man-pages/tshark.html) |
| 20 Linux Networking | [Linux kernel networking documentation](https://docs.kernel.org/networking/index.html), [iproute2 source/docs](https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/), [nftables wiki](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page), Linux `ip(8)`, `ss(8)`, `bridge(8)`, `network_namespaces(7)` manual pages |
| 21 Troubleshooting | Protocol references above, Wireshark documentation, Linux manuals, and controlled two-sided captures |
| 22–23 Interviews and Quiz | Review and assessment synthesized from chapters 00–21 and their references |

## Recommended books

### Best starting point

1. **Computer Networking: A Top-Down Approach** — James F. Kurose and Keith W. Ross. Starts with applications and moves down the stack; strong for beginners and university study.
2. **Computer Networks: A Systems Approach** — Larry L. Peterson and Bruce S. Davie. Available as an open book at [systemsapproach.org](https://book.systemsapproach.org/); strong systems and implementation perspective.

### Packet-level depth

3. **TCP/IP Illustrated, Volume 1, Second Edition** — Kevin R. Fall and W. Richard Stevens. Detailed protocol behavior demonstrated through real traces.
4. **The Illustrated Network** — Walter Goralski. Uses a consistent working topology to explain TCP/IP visually.

### Network design and operations

5. **Top-Down Network Design** — Priscilla Oppenheimer. Requirements-driven enterprise design.
6. **Network Warrior** — Gary A. Donahue. Practical switching, routing, and operations; vendor-oriented examples should be checked against current documentation.
7. **Troubleshooting with Wireshark** — Laura Chappell. Workflow-focused packet analysis; verify current UI/filter details in the official guide.

### Linux and performance

8. **Linux Networking Cookbook** — practical Linux network configuration and service examples; confirm commands against current distro documentation.
9. **Systems Performance** — Brendan Gregg. Broader performance methodology, observability, latency, and evidence-based diagnosis.

## Official tool documentation

### Wireshark and packet capture

- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [Wireshark display-filter manual](https://www.wireshark.org/docs/man-pages/wireshark-filter.html)
- [Display Filter Reference](https://www.wireshark.org/docs/dfref/)
- [libpcap capture filter syntax](https://www.tcpdump.org/manpages/pcap-filter.7.html)
- [tcpdump manual](https://www.tcpdump.org/manpages/tcpdump.1.html)

### Linux

- [Linux kernel networking documentation](https://docs.kernel.org/networking/index.html)
- [Linux IP sysctl reference](https://docs.kernel.org/networking/ip-sysctl.html)
- [iproute2 repository](https://git.kernel.org/pub/scm/network/iproute2/iproute2.git/)
- [nftables documentation](https://wiki.nftables.org/)
- [man7.org networking manuals](https://man7.org/linux/man-pages/man7/network_namespaces.7.html)

## Platform references for upcoming chapters

### Docker

- [Docker networking overview](https://docs.docker.com/engine/network/)
- [Network drivers](https://docs.docker.com/engine/network/drivers/)
- [Bridge driver](https://docs.docker.com/engine/network/drivers/bridge/)
- [Port publishing](https://docs.docker.com/engine/network/port-publishing/)
- [Compose networking](https://docs.docker.com/compose/how-tos/networking/)

### Kubernetes

- [Services, Load Balancing, and Networking](https://kubernetes.io/docs/concepts/services-networking/)
- [Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)
- [Services](https://kubernetes.io/docs/concepts/services-networking/service/)
- [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
- [NetworkPolicy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
- [IPv4/IPv6 dual stack](https://kubernetes.io/docs/concepts/services-networking/dual-stack/)

### AWS VPC

- [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)
- [VPC route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
- [Security groups](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)
- [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html)
- [Internet gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)
- [NAT gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)
- [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

## Research and verification policy

Before changing technical content:

1. Check the current RFC/status or official platform documentation.
2. Confirm whether behavior is standard, vendor-specific, OS-specific, or an inference.
3. Reproduce commands in an isolated lab where feasible.
4. Document environment and capture point.
5. Prefer paraphrase and original diagrams; do not copy large passages or proprietary figures.
6. Add a direct reference near claims that are version-sensitive or platform-specific.

## Corrections

Standards and platforms evolve. Open an issue or pull request with the exact claim, authoritative source, affected chapter, and proposed correction.
