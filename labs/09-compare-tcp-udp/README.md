# Lab 09 — Compare TCP and UDP

[← Labs](../README.md) · [TCP](../../docs/09-TCP/README.md) · [UDP](../../docs/10-UDP/README.md) · [Ports](../../docs/11-Ports/README.md)

## Objective

Create loopback-only TCP and UDP listeners, inspect sockets, exchange controlled data, and compare captures. No external scan is performed.

## Prerequisites

Linux/WSL with `nc`, `ss`, and `tcpdump`. Netcat syntax differs; `ncat` or OpenBSD netcat is recommended.

## TCP test

Terminal A:

```bash
nc -l 127.0.0.1 9001
```

Terminal B:

```bash
printf 'hello over tcp\n' | nc 127.0.0.1 9001
```

Before/during connection:

```bash
ss -ltnp 'sport = :9001'
sudo tcpdump -ni lo -c 20 'tcp port 9001'
```

Observe SYN/SYN-ACK/ACK, data ACKs, and close behavior.

## UDP test

Terminal A:

```bash
nc -u -l 127.0.0.1 9002
```

Terminal B:

```bash
printf 'one udp datagram\n' | nc -u -w 1 127.0.0.1 9002
```

Inspect:

```bash
ss -lunp 'sport = :9002'
sudo tcpdump -ni lo -c 5 'udp port 9002 or icmp'
```

Observe no transport handshake and one datagram boundary.

## Failure comparison

After stopping listeners:

```bash
nc -vz -w 2 127.0.0.1 9001
printf x | nc -u -w 1 127.0.0.1 9002
```

Capture TCP RST and possible ICMP Port Unreachable. Tool output alone is less reliable for UDP.

## Verification checklist

- [ ] Listener bound only to loopback.
- [ ] TCP handshake identified.
- [ ] UDP delivered without handshake.
- [ ] Full source/destination ports recorded.
- [ ] Closed-port behaviors compared.

## Troubleshooting

- Netcat exits immediately: check implementation-specific listen syntax.
- Process hidden in `ss`: use authorized privilege.
- No Ethernet header: loopback has a different link type; expected.
- UDP reports success with no listener: expected limitation of connectionless probing; inspect ICMP/capture.

## Cleanup

Stop both listeners with `Ctrl+C`. No persistent configuration is changed.

## Reflection questions

1. Which packets exist only for TCP?
2. How did closed-port evidence differ?
3. Why did loopback binding limit exposure?
4. Which application features would UDP need for reliable ordered delivery?
