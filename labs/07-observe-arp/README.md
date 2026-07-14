# Lab 07 — Observe ARP Resolution

[← Labs](../README.md) · [ARP chapter](../../docs/07-ARP/README.md)

## Objective

Identify the default next hop, observe ARP request/reply fields, and correlate them with Linux neighbor state without flushing production caches.

## Steps

```bash
ip route get 1.1.1.1
ip neighbor
```

Record gateway and interface. Then replace `IFACE` and `GATEWAY`:

```bash
sudo tcpdump -eni IFACE -c 20 arp
```

In another terminal, use an authorized local probe:

```bash
arping -c 3 -I IFACE GATEWAY
```

If `arping` is unavailable, ordinary gateway traffic may produce ARP when the cache needs refresh. Do not flush a shared/production cache merely to force traffic.

## Verify

- Ethernet request destination is broadcast.
- ARP sender fields identify your interface.
- Target IP is the gateway.
- Reply supplies the gateway MAC.
- `ip neighbor show GATEWAY` reflects a usable mapping/state.

## Wireshark filter

```text
arp.addr.proto_ipv4 == GATEWAY_IP
```

Replace the placeholder with the numeric gateway IP.

## Troubleshooting

- No packets: cache may already be valid or wrong interface selected.
- No reply: verify VLAN, prefix, link, gateway availability, and capture direction.
- Different reply MAC: HA, proxy ARP, virtualization, or an unexpected device may be involved; investigate rather than assuming.

## Cleanup

No persistent configuration changed.

## Reflection questions

1. Why was the remote test address absent from the ARP request?
2. Which fields let the target reply unicast?
3. What legitimate events can change the gateway mapping?
