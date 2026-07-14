# Lab 16 — Observe IPv6 and NDP

[← Labs](../README.md) · [IPv6 chapter](../../docs/18-IPv6/README.md)

## Objective

Create two IPv6 hosts on an isolated link, observe automatic link-local addresses, NDP, neighbor state, and global-prefix communication.

## Build

```bash
sudo ip netns add v6-a
sudo ip netns add v6-b
sudo ip link add va type veth peer name vb
sudo ip link set va netns v6-a
sudo ip link set vb netns v6-b
sudo ip -n v6-a link set lo up; sudo ip -n v6-b link set lo up
sudo ip -n v6-a link set va up; sudo ip -n v6-b link set vb up
sudo ip -n v6-a addr add 2001:db8:1::10/64 dev va
sudo ip -n v6-b addr add 2001:db8:1::20/64 dev vb
```

## Observe

```bash
sudo ip -n v6-a -6 address
sudo ip -n v6-a -6 route
sudo ip netns exec v6-a ping -6 -c 2 2001:db8:1::20
sudo ip -n v6-a -6 neighbor
```

Capture NDP while clearing/relearning the neighbor:

```bash
sudo ip -n v6-a -6 neighbor flush dev va
sudo ip netns exec v6-a tcpdump -ni va icmp6
```

In another terminal repeat the ping and identify Neighbor Solicitation/Advertisement.

## Verification

- Both interfaces have `fe80::/10` link-local addresses.
- The connected `2001:db8:1::/64` route exists.
- Neighbor state becomes reachable/stale after traffic.
- No ARP packets are used.

## Cleanup

```bash
sudo ip netns del v6-a
sudo ip netns del v6-b
```

## Reflection questions

1. Which multicast address received Neighbor Solicitation?
2. Why is no default route required for the shared `/64`?
3. How would Router Advertisements add a default route?
