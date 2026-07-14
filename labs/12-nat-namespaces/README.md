# Lab 12 — Build NAT with Linux Network Namespaces

[← Labs](../README.md) · [NAT chapter](../../docs/14-NAT/README.md)

## Objective

Create an isolated client, NAT gateway, and server using Linux namespaces; observe the source tuple before and after SNAT without altering the host's main route.

## Prerequisites

- Disposable Linux VM with root/sudo.
- `ip`, `nft`, `python3`, `curl`, and `tcpdump`.
- Do not run on a production or remote-only host.

## Topology

```text
client 10.10.0.2/24 ─ gateway 10.10.0.1 | 198.51.100.1 ─ server 198.51.100.2/24
```

All addresses are isolated documentation/lab space.

## 1. Create namespaces and links

```bash
sudo ip netns add nat-client
sudo ip netns add nat-gw
sudo ip netns add nat-server

sudo ip link add c0 type veth peer name g0
sudo ip link add g1 type veth peer name s0
sudo ip link set c0 netns nat-client
sudo ip link set g0 netns nat-gw
sudo ip link set g1 netns nat-gw
sudo ip link set s0 netns nat-server
```

## 2. Configure addresses and routes

```bash
sudo ip -n nat-client addr add 10.10.0.2/24 dev c0
sudo ip -n nat-gw addr add 10.10.0.1/24 dev g0
sudo ip -n nat-gw addr add 198.51.100.1/24 dev g1
sudo ip -n nat-server addr add 198.51.100.2/24 dev s0

for ns in nat-client nat-gw nat-server; do sudo ip -n "$ns" link set lo up; done
sudo ip -n nat-client link set c0 up
sudo ip -n nat-gw link set g0 up
sudo ip -n nat-gw link set g1 up
sudo ip -n nat-server link set s0 up

sudo ip -n nat-client route add default via 10.10.0.1
sudo ip -n nat-server route add default via 198.51.100.1
sudo ip netns exec nat-gw sysctl -w net.ipv4.ip_forward=1
```

## 3. Add isolated NAT policy

```bash
sudo ip netns exec nat-gw nft add table ip lab
sudo ip netns exec nat-gw nft 'add chain ip lab postrouting { type nat hook postrouting priority srcnat; policy accept; }'
sudo ip netns exec nat-gw nft add rule ip lab postrouting ip saddr 10.10.0.0/24 oifname g1 masquerade
```

## 4. Run a service and capture

Terminal A:

```bash
sudo ip netns exec nat-server python3 -m http.server 8080 --bind 198.51.100.2
```

Terminal B, private side:

```bash
sudo ip netns exec nat-gw tcpdump -ni g0 tcp port 8080
```

Terminal C, server side:

```bash
sudo ip netns exec nat-gw tcpdump -ni g1 tcp port 8080
```

Terminal D:

```bash
sudo ip netns exec nat-client curl http://198.51.100.2:8080/
```

Compare source address/port on `g0` and `g1`. The server side should see the gateway's `198.51.100.1` source.

## 5. Inspect state

```bash
sudo ip netns exec nat-gw nft list ruleset
sudo ip netns exec nat-gw conntrack -L 2>/dev/null
```

`conntrack` is optional and may require package installation.

## Verification checklist

- [ ] Client routed through `nat-gw`.
- [ ] Server returned the HTTP response.
- [ ] Private capture showed `10.10.0.2` source.
- [ ] External capture showed translated `198.51.100.1` source.
- [ ] Return traffic reached the client.

## Troubleshooting

- **No forwarding:** verify namespace `ip_forward`, routes, and link state.
- **No translation:** inspect nft rule counters and interface name.
- **Connection refused:** ensure Python server is listening in `nat-server`.
- **Reply missing:** verify server default route through `198.51.100.1`.
- **nft syntax differs:** use a current nftables-capable disposable Linux environment.

## Cleanup

This removes all lab links, processes, rules, and namespaces:

```bash
sudo ip netns pids nat-server | xargs -r sudo kill
sudo ip netns del nat-client
sudo ip netns del nat-gw
sudo ip netns del nat-server
```

## Reflection questions

1. Which tuple changed and which destination fields remained?
2. Why was the server default route required?
3. What would fail if the reply bypassed `nat-gw`?
4. How would you extend the lab with DNAT port forwarding?
