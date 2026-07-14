# Lab 06 — Inspect Layer 2 Identity

[← Labs](../README.md) · [MAC Address chapter](../../docs/06-MAC-Address/README.md)

## Objective

Inventory local link-layer addresses, distinguish physical and virtual interfaces, identify the default gateway neighbor, and observe Ethernet endpoints without changing configuration.

## Prerequisites

- Linux or WSL with `ip` and `tcpdump`.
- Permission to capture traffic.

## 1. Inventory links

```bash
ip -brief link
```

Record interface name, state, and MAC. Classify loopback, physical/Wi-Fi, bridge, container, VPN, and tunnel interfaces where possible. Do not assume every interface uses a 48-bit Ethernet address.

## 2. Find the next hop

```bash
ip route get 1.1.1.1
ip neighbor
```

Find the `via` gateway and match its IP to a neighbor-table entry. If absent, generate permitted traffic and check again.

## 3. Observe frame endpoints

Replace `IFACE`:

```bash
sudo tcpdump -eni IFACE -c 12 'arp or icmp'
```

In another terminal:

```bash
ping -c 2 1.1.1.1
```

Compare Ethernet destination with IP destination. For a remote IP, they should represent the gateway and remote endpoint respectively.

## 4. Optional Linux bridge inspection

```bash
bridge link 2>/dev/null
bridge fdb show 2>/dev/null
```

Container hosts may show bridge forwarding entries. On a normal workstation, output may be empty.

## Verification checklist

- [ ] Identified each local interface MAC/link type.
- [ ] Distinguished locally administered addresses where applicable.
- [ ] Found default route and gateway neighbor.
- [ ] Compared link-layer and IP destinations.
- [ ] Documented capture interface and environment.

## Troubleshooting

- **No neighbor entry:** it may have expired; generate authorized local traffic.
- **No Ethernet header:** loopback, tunnel, Linux cooked capture, or another link type is in use.
- **Gateway state `INCOMPLETE`/`FAILED`:** investigate link, VLAN, address/prefix, and gateway availability.
- **WSL gateway differs from home router:** WSL commonly uses a virtual NAT gateway.

## Cleanup

None. Commands are read-only and the capture is not written to disk.

## Reflection questions

1. Which MAC addresses appear locally administered?
2. Does the remote IP ever appear as the Ethernet destination?
3. Which interfaces belong to virtualization or containers?
4. Why would the switch and host maintain different tables?
