# Lab 13 — Build VLANs with Network Namespaces

[← Labs](../README.md) · [VLAN chapter](../../docs/15-VLAN/README.md)

## Objective

Create VLAN 10 and VLAN 20 over one isolated trunk and verify Layer 2 separation.

## Prerequisites and safety

Use a disposable Linux VM with `ip` and root access. The lab creates only namespaces/veth devices.

## Build the trunk

```bash
sudo ip netns add va
sudo ip netns add vb
sudo ip link add ta type veth peer name tb
sudo ip link set ta netns va
sudo ip link set tb netns vb
sudo ip -n va link set lo up
sudo ip -n vb link set lo up
sudo ip -n va link set ta up
sudo ip -n vb link set tb up
```

## Add VLAN subinterfaces

```bash
sudo ip -n va link add link ta name ta.10 type vlan id 10
sudo ip -n va link add link ta name ta.20 type vlan id 20
sudo ip -n vb link add link tb name tb.10 type vlan id 10
sudo ip -n vb link add link tb name tb.20 type vlan id 20
sudo ip -n va addr add 192.0.2.10/24 dev ta.10
sudo ip -n vb addr add 192.0.2.20/24 dev tb.10
sudo ip -n va addr add 198.51.100.10/24 dev ta.20
sudo ip -n vb addr add 198.51.100.20/24 dev tb.20
for x in ta.10 ta.20; do sudo ip -n va link set "$x" up; done
for x in tb.10 tb.20; do sudo ip -n vb link set "$x" up; done
```

## Verify

```bash
sudo ip netns exec va ping -c 2 192.0.2.20
sudo ip netns exec va ping -c 2 198.51.100.20
sudo ip netns exec va tcpdump -eni ta vlan
```

The first ping uses VLAN 10 and succeeds. The second destination belongs to VLAN 20 but the source selection/routing context matters; test explicitly with `ping -I 198.51.100.10` to observe VLAN 20.

## Troubleshooting

- Verify IDs with `ip -n va -d link show`.
- Both sides must use the same VLAN ID.
- Confirm subinterfaces are UP and addresses have correct prefixes.
- Capture parent `ta`/`tb` to see tags; child capture may show de-tagged traffic.

## Cleanup

```bash
sudo ip netns del va
sudo ip netns del vb
```

## Reflection questions

1. Where were tags visible?
2. Could VLAN 10 reach VLAN 20 without a router?
3. How would a native VLAN mismatch appear?
