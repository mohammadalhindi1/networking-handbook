# Lab 14 — Observe Linux Bridge Learning

[← Labs](../README.md) · [Switching chapter](../../docs/16-Switching/README.md)

## Objective

Build a software switch with two isolated hosts and observe MAC learning in its forwarding database.

## Build

```bash
sudo ip netns add sw-a
sudo ip netns add sw-b
sudo ip link add a0 type veth peer name a-sw
sudo ip link add b0 type veth peer name b-sw
sudo ip link set a0 netns sw-a
sudo ip link set b0 netns sw-b
sudo ip link add br-lab type bridge
sudo ip link set a-sw master br-lab
sudo ip link set b-sw master br-lab
sudo ip link set br-lab up
sudo ip link set a-sw up
sudo ip link set b-sw up
sudo ip -n sw-a addr add 192.0.2.10/24 dev a0
sudo ip -n sw-b addr add 192.0.2.20/24 dev b0
sudo ip -n sw-a link set lo up
sudo ip -n sw-b link set lo up
sudo ip -n sw-a link set a0 up
sudo ip -n sw-b link set b0 up
```

## Observe learning

```bash
bridge fdb show br br-lab
sudo ip netns exec sw-a ping -c 2 192.0.2.20
bridge fdb show br br-lab
```

Match dynamically learned MACs to `a-sw` and `b-sw`. Use `ip -n sw-a link show a0` to confirm endpoint MACs.

## Experiment

```bash
sudo bridge fdb flush br br-lab
bridge fdb show br br-lab
sudo ip netns exec sw-a ping -c 1 192.0.2.20
bridge fdb show br br-lab
```

The first exchange after a flush requires flooding/ARP and relearning.

## Cleanup

```bash
sudo ip netns del sw-a
sudo ip netns del sw-b
sudo ip link del br-lab
```

## Reflection questions

1. Which source frame taught each entry?
2. What happens before the destination is known?
3. How is the FDB different from `ip neighbor`?
