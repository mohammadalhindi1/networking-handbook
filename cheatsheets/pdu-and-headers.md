# PDU and Header Quick Reference

[Home](../README.md) · [Encapsulation chapter](../docs/03-Data-Encapsulation/README.md) · [Labs](../labs/README.md)

## Encapsulation chain

```text
Application data
└─ TCP header + data                       → TCP segment
   └─ IPv4 header + segment                → IP packet
      └─ Ethernet header + packet + FCS    → Ethernet frame
         └─ encoded on the medium          → bits/signals
```

UDP creates a **datagram** instead of a TCP segment. IPv6 uses a fixed 40-byte base header plus optional extension headers.

## Common minimum sizes

| Item | Typical size | Notes |
|---|---:|---|
| Ethernet II header | 14 bytes | Destination MAC, source MAC, EtherType |
| 802.1Q VLAN tag | 4 bytes | Inserted into Ethernet header |
| Ethernet FCS | 4 bytes | Often absent from host packet captures |
| IPv4 header | 20 bytes | Without options |
| IPv6 base header | 40 bytes | Extension headers are separate |
| TCP header | 20 bytes | Without options; SYN commonly carries options |
| UDP header | 8 bytes | Fixed size |
| Ethernet payload | 46–1500 bytes | Standard Ethernet; smaller payloads are padded |

## What normally changes at a router?

| Information | Normal routed forwarding |
|---|---|
| Ethernet source/destination | Replaced for the outgoing link |
| Ethernet FCS | Recalculated |
| IPv4 TTL / IPv6 Hop Limit | Decreased by one |
| IPv4 header checksum | Recalculated because TTL changed |
| End-to-end IP addresses | Preserved unless NAT/tunneling applies |
| TCP/UDP ports | Preserved unless translation/proxying applies |
| Application payload | Preserved unless a proxy terminates the flow |

## Useful Wireshark fields

| Purpose | Field or filter |
|---|---|
| Frame size | `frame.len` |
| Ethernet endpoints | `eth.src`, `eth.dst` |
| EtherType | `eth.type` |
| IP endpoints | `ip.src`, `ip.dst`, `ipv6.src`, `ipv6.dst` |
| IPv4 total length | `ip.len` |
| IPv4 header length | `ip.hdr_len` |
| IPv6 payload length | `ipv6.plen` |
| TCP header length | `tcp.hdr_len` |
| TCP payload length | `tcp.len` |
| UDP length | `udp.length` |
| Fragmentation | `ip.flags.mf == 1 or ip.frag_offset > 0` |

## Fast calculations

For a basic IPv4/TCP packet without options:

```text
TCP payload = IPv4 total length − IPv4 header length − TCP header length
            = IPv4 total length − 20 − 20
```

Do not assume all headers are minimal. Read the actual header-length fields.
