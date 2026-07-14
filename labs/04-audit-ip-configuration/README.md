# Lab 04 — Audit Local IP Configuration

[← Labs](../README.md) · [IP Addressing chapter](../../docs/04-IP-Addressing/README.md)

## Objective

Create a read-only inventory of local addresses, scopes, prefixes, routes, and source-selection decisions. No configuration changes are required.

## Prerequisites

- Linux or WSL with the `ip` command.
- Optional: `dig` and `ping` for comparison tests.

## 1. Inventory interfaces

```bash
ip -brief link
ip -brief address
```

For each non-loopback interface, record state, IPv4/IPv6 addresses, and prefixes. Label physical, Wi-Fi, container, VPN, bridge, and tunnel interfaces when recognizable.

## 2. Inspect one interface deeply

```bash
ip address show dev IFACE
```

Record address scope, dynamic/permanent flags, and lifetimes. Replace `IFACE` with an interface from step 1.

## 3. Read route tables and rules

```bash
ip route
ip -6 route
ip rule
```

Identify connected routes, default routes, gateways, metrics, and any additional routing tables.

## 4. Ask the kernel for decisions

```bash
ip route get 127.0.0.1
ip route get 1.1.1.1
ip route get 192.0.2.1
```

The documentation address may follow your default route even though no host responds. Route lookup does not prove reachability.

For each result, record destination, next hop, device, and selected source.

## 5. Separate IP and DNS tests

```bash
ping -c 2 1.1.1.1
dig example.com
```

If ping is filtered, document that the result is inconclusive. DNS success and ICMP success test different behavior.

## 6. Classify addresses

For every IPv4 address found, classify it as loopback, private, link-local, shared, documentation, multicast, or public candidate. Do not publish your actual public address in lab notes.

## Verification checklist

- [ ] Identified all interfaces and address families.
- [ ] Recorded prefixes, not only addresses.
- [ ] Identified default route(s) and gateways.
- [ ] Compared route decisions for three destinations.
- [ ] Classified address scopes correctly.
- [ ] Removed or anonymized sensitive addresses before sharing output.

## Troubleshooting

- **Several default routes:** compare metrics, policy rules, VPN state, and address families.
- **No default route:** the system may be isolated, use policy routing, or lack configuration.
- **`unreachable` result:** inspect route tables and rules; route lookup failed before packets were sent.
- **Unexpected interface:** VPNs, containers, and policy routing can change selection.
- **WSL NAT address:** WSL commonly uses a virtual/NAT network different from the Windows host.

## Cleanup

None. All commands are read-only.

## Reflection questions

1. Which destination used loopback, a connected route, and the default route?
2. Did IPv4 and IPv6 use the same interface and gateway?
3. Which address would Linux select as source for an Internet destination?
4. Why is a route lookup useful even when the destination does not respond?
