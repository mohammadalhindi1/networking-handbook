# Lab 01 — Observe Basic Connectivity

[← Labs](../README.md) · [Chapter 00](../../docs/00-Networking-Basics/README.md)

## Objective

Inspect local network configuration, identify the route used for a remote destination, measure reachability, and observe ICMP packets.

## Prerequisites

- Linux or WSL with `ip`, `ping`, and `tcpdump` installed.
- Permission to capture traffic (`sudo` is commonly required).
- A network where ICMP testing is allowed.

## Topology

```mermaid
flowchart LR
    H["Your host"] --> G["Default gateway"]
    G --> D["Remote destination"]
```

## Steps

### 1. Record interface state

```bash
ip -brief address
```

Identify the active interface and its IP prefix. Ignore `lo` for the remote test.

### 2. Inspect routes

```bash
ip route
ip route get 1.1.1.1
```

The second command asks the kernel which route, gateway, interface, and source address it would use. It does not send the application packet itself.

### 3. Test the loopback and remote paths

```bash
ping -c 4 127.0.0.1
ping -c 4 1.1.1.1
```

The loopback test checks the local IP stack. The remote test also depends on the interface, local network, routing path, and remote ICMP policy.

### 4. Capture only ICMP

In terminal A, replace `eth0` with the active interface from step 1:

```bash
sudo tcpdump -ni eth0 -c 8 icmp
```

In terminal B:

```bash
ping -c 4 1.1.1.1
```

`-n` avoids DNS lookups, `-i` selects the interface, `-c 8` stops after eight packets, and `icmp` is the capture filter. A successful four-packet test normally produces four requests and four replies.

## Expected observations

- Loopback replies have very low RTT because packets do not leave the host.
- `ip route get` identifies a source address and egress interface.
- Each successful ping has an Echo Request and matching Echo Reply.
- Sequence numbers increase, and `ping` reports loss and RTT statistics.

Exact addresses, interface names, TTL values, and times will vary.

## Verification

You have completed the lab when you can answer:

- Which interface carries remote traffic?
- Is a gateway used for the destination?
- What are the request and reply IP addresses?
- How many packets were lost?
- What is the average RTT?

## Troubleshooting

- **`ping: command not found`:** install your distribution's `iputils-ping` package.
- **`tcpdump: command not found`:** install the `tcpdump` package.
- **Permission denied during capture:** use an authorized account and `sudo`.
- **No remote replies:** confirm general connectivity and remember that ICMP can be filtered. Try a destination permitted by your environment.
- **WSL capture differs from Windows traffic:** WSL uses a virtualized network path; capture inside the environment whose traffic you are testing.

## Cleanup

No persistent configuration was changed. `tcpdump` exits automatically after the packet count or with `Ctrl+C`.

## Reflection questions

1. Why does a successful loopback ping not prove Internet connectivity?
2. What extra dependencies are introduced by testing a hostname instead of an IP?
3. If Echo Requests appear but replies do not, what has the capture proven—and what has it not proven?
