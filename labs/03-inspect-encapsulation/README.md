# Lab 03 — Inspect Encapsulation

[← Labs](../README.md) · [Encapsulation chapter](../../docs/03-Data-Encapsulation/README.md)

## Objective

Capture a controlled HTTPS connection, identify nested protocol headers, calculate payload sizes, and relate observed packet length to interface MTU.

## Prerequisites

- Linux or WSL with `ip`, `curl`, `tcpdump`, and Wireshark or `tshark`.
- Permission to capture on the selected interface.
- Read [Lab 02](../02-observe-tcp-ip-stack/README.md) first.

## Safety

Use only `example.com`, stop immediately after the request, and delete captures containing unrelated traffic. Packet captures can expose metadata and payloads.

## 1. Record the environment

```bash
ip -brief link
ip link show
tcpdump --version
```

Record the active interface and its MTU. Do not assume it is `eth0` or 1500.

## 2. Capture a single connection

Replace `IFACE` with the active interface:

```bash
sudo tcpdump -ni IFACE -s 0 -c 40 -w encapsulation.pcap \
  'host example.com and (tcp port 443 or udp port 443)'
```

In another terminal:

```bash
curl --http1.1 -sS https://example.com/ -o /dev/null
```

`--http1.1` makes TCP/TLS more likely and avoids HTTP/3 for this exercise. DNS may resolve before `tcpdump` compiles the hostname filter; if the filter fails, resolve the address first and use `host ADDRESS`.

Stop the capture if it does not reach 40 packets.

## 3. Inspect protocol nesting

Open the capture in Wireshark and select a TCP packet containing TLS application data. Expand:

1. Frame
2. Ethernet II or the link type shown in your environment
3. IPv4 or IPv6
4. TCP
5. TLS

Not every capture uses Ethernet. Loopback, tunnels, Wi-Fi monitor mode, and Linux cooked capture have different link headers.

## 4. Calculate payload

For TCP over IPv4, record:

- `ip.len`
- `ip.hdr_len`
- `tcp.hdr_len`
- `tcp.len`

Verify:

```text
tcp.len = ip.len − ip.hdr_len − tcp.hdr_len
```

For IPv6, account for the 40-byte base header and any extension headers rather than applying the IPv4 formula blindly.

## 5. Inspect raw bytes

```bash
tcpdump -nn -r encapsulation.pcap -c 3 -XX
```

Match the decoded Ethernet/IP/TCP fields with bytes in the hex view. Multi-byte Internet protocol fields use network byte order (big-endian).

## 6. Compare with MTU and offload

```bash
ip link show dev IFACE
sudo ethtool -k IFACE 2>/dev/null | head -n 30
```

If captured outgoing packets appear larger than MTU or checksums appear invalid, document possible segmentation/checksum offload. Do not change production settings for a lab.

## Wireshark filters

```text
tcp.stream eq 0
tcp.len > 0
ip.flags.mf == 1 or ip.frag_offset > 0
tcp.analysis.retransmission
```

## Verification checklist

- [ ] Recorded interface, link type, and MTU.
- [ ] Identified link, IP, TCP, and TLS layers in one packet.
- [ ] Verified TCP payload length using actual header fields.
- [ ] Located source/destination addresses and ports.
- [ ] Checked for fragmentation, retransmission, and offload clues.
- [ ] Sanitized or deleted the capture.

## Troubleshooting

- **Zero captured packets:** the hostname resolved to another address family or the wrong interface was selected.
- **No Ethernet header:** the capture link type differs; describe what is actually present.
- **No TLS data:** increase packet count or start capture before running `curl`.
- **Checksum warning:** compare direction and offload features before concluding corruption.
- **Packets larger than MTU:** investigate host-side TSO/GSO and the capture point.

## Cleanup

```bash
rm -f encapsulation.pcap
```

## Reflection questions

1. Which layer's destination identifies the next hop, and which identifies the remote endpoint?
2. How many bytes of headers surrounded the selected TCP payload?
3. Was the Ethernet FCS visible? Why might it be absent?
4. How would a VLAN tag or VPN tunnel change the nesting and overhead?
