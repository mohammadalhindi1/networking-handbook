# Lab 11 — Audit a DHCP Lease

[← Labs](../README.md) · [DHCP chapter](../../docs/13-DHCP/README.md)

## Objective

Identify how an interface received its configuration, inspect the applied lease/options, and optionally observe DHCP traffic without forcing a disconnect.

## Safety

Do not release or renew a lease on a remote server, shared lab, or production host. This lab is read-only unless a natural renewal occurs.

## 1. Select an interface

```bash
ip -brief address
ip route
```

Choose the interface carrying the default route.

## 2. Inspect applied configuration

Use the manager installed on your system:

```bash
nmcli device show IFACE
networkctl status IFACE
resolvectl status IFACE
```

Record address/prefix, gateway, DNS, lease/server data if exposed, and whether the address is marked dynamic.

## 3. Inspect logs safely

```bash
journalctl -u NetworkManager --since today 2>/dev/null
journalctl -u systemd-networkd --since today 2>/dev/null
```

Filter locally for the chosen interface and avoid publishing private infrastructure information.

## 4. Optional passive capture

```bash
sudo tcpdump -ni IFACE -vv 'udp port 67 or udp port 68'
```

Wait for natural renewal traffic. Do not generate DHCP floods or disrupt other clients. A short lab may capture nothing when T1 is far away; that is expected.

## 5. Analyze an authorized capture

In Wireshark use `dhcp`. Identify message type, transaction ID, client/server identifiers, offered address, relay address, lease/T1/T2, mask, router, and DNS options.

## Verification checklist

- [ ] Identified configuration manager and interface.
- [ ] Recorded address, prefix, route, and resolver.
- [ ] Determined dynamic versus manual assignment.
- [ ] Located lease evidence or documented why unavailable.
- [ ] Did not force a disconnect.
- [ ] Sanitized screenshots/logs/captures.

## Troubleshooting questions

1. Does the client send Discover?
2. Does any Offer return on the correct VLAN?
3. Does the offer contain the expected scope/options?
4. Does Request select the expected server/address?
5. Does ACK arrive and does the client install it?
6. Do gateway, DNS, and application tests work independently?

## Cleanup

Stop `tcpdump` with `Ctrl+C`. No network configuration was changed.

## Reflection questions

1. Which DHCP data became an address, route, and resolver setting?
2. How would a relay change the capture and scope decision?
3. Why can a valid lease coexist with application failure?
4. Which controls prevent an unauthorized server on your network?
