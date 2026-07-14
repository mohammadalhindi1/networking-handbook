# Lab 15 — Build a Routed Topology

[← Labs](../README.md) · [Routing chapter](../../docs/17-Routing/README.md)

## Objective

Connect two isolated subnets through a Linux router and demonstrate the need for forwarding and reverse routes.

## Build

```bash
sudo ip netns add r-a; sudo ip netns add r-gw; sudo ip netns add r-b
sudo ip link add a0 type veth peer name ga
sudo ip link add b0 type veth peer name gb
sudo ip link set a0 netns r-a; sudo ip link set ga netns r-gw
sudo ip link set b0 netns r-b; sudo ip link set gb netns r-gw
sudo ip -n r-a addr add 192.0.2.10/24 dev a0
sudo ip -n r-gw addr add 192.0.2.1/24 dev ga
sudo ip -n r-b addr add 198.51.100.20/24 dev b0
sudo ip -n r-gw addr add 198.51.100.1/24 dev gb
for x in r-a r-gw r-b; do sudo ip -n "$x" link set lo up; done
sudo ip -n r-a link set a0 up; sudo ip -n r-gw link set ga up
sudo ip -n r-b link set b0 up; sudo ip -n r-gw link set gb up
sudo ip -n r-a route add default via 192.0.2.1
sudo ip -n r-b route add default via 198.51.100.1
sudo ip netns exec r-gw sysctl -w net.ipv4.ip_forward=1
```

## Verify

```bash
sudo ip netns exec r-a ip route get 198.51.100.20
sudo ip netns exec r-a ping -c 2 198.51.100.20
sudo ip netns exec r-gw tcpdump -ni any icmp
```

Delete B's default route temporarily to observe one-way failure, then restore it:

```bash
sudo ip -n r-b route del default
sudo ip netns exec r-a ping -c 2 198.51.100.20
sudo ip -n r-b route add default via 198.51.100.1
```

## Cleanup

```bash
sudo ip netns del r-a; sudo ip netns del r-gw; sudo ip netns del r-b
```

## Reflection questions

1. Which routes were connected and which were default?
2. What did the missing reverse route look like in the capture?
3. Which header changed at the router?
