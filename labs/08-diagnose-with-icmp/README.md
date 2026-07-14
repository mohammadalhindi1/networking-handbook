# Lab 08 — Diagnose with ICMP

[← Labs](../README.md) · [ICMP chapter](../../docs/08-ICMP/README.md)

## Objective

Compare ICMP reachability, hop discovery, and an actual HTTPS service test without treating any single result as final proof.

## Steps

### Establish scope

```bash
ip route
ping -c 2 127.0.0.1
```

### Test gateway and external IP

Replace `GATEWAY` from the default route:

```bash
ping -c 3 GATEWAY
ping -c 4 1.1.1.1
```

### Compare the real application

```bash
curl -I --connect-timeout 5 https://example.com/
```

### Trace using two probe styles

```bash
traceroute -m 12 example.com
sudo traceroute -T -p 443 -m 12 example.com
```

Differences can result from policy and load balancing.

### Optional PMTU observation

```bash
tracepath example.com
```

Do not infer an exact path MTU if the environment filters required feedback.

## Capture

```bash
sudo tcpdump -ni IFACE -c 30 'icmp or icmp6'
```

Look for Echo, Time Exceeded, Unreachable, and Packet Too Big/fragmentation-needed messages.

## Verification checklist

- [ ] Distinguished loopback, gateway, remote IP, and HTTPS tests.
- [ ] Recorded loss and RTT without overgeneralizing.
- [ ] Compared default and TCP traceroute behavior.
- [ ] Explained stars using later-hop evidence.
- [ ] Identified any ICMP error's embedded original flow.

## Cleanup

No persistent changes. Stop captures with `Ctrl+C`.

## Reflection questions

1. Did ping and HTTPS agree? What does each prove?
2. Did probe type change the visible path?
3. Why can intermediate loss be misleading?
4. What breaks when Packet Too Big feedback is blocked?
