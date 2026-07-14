# Lab 10 — Trace DNS Resolution

[← Labs](../README.md) · [DNS chapter](../../docs/12-DNS/README.md)

## Objective

Compare the system resolver with direct DNS queries, trace delegation, observe cache behavior, and inspect DNS packet fields.

## Prerequisites

- Linux or WSL with `dig`, `getent`, and `tcpdump`.
- Permission to capture traffic.
- Use only public documentation names.

## 1. Inspect resolver configuration

```bash
resolvectl status 2>/dev/null || cat /etc/resolv.conf
```

Record servers and per-link/search domains without publishing private infrastructure addresses.

## 2. Compare resolution paths

```bash
getent ahosts example.com
dig example.com A
dig example.com AAAA
```

`getent` follows system name-service policy; `dig` sends a DNS query. Differences are evidence, not automatically errors.

## 3. Trace delegation

```bash
dig +trace example.com
```

Identify root, TLD referral, authoritative referral, and final answer. Some networks block direct DNS to arbitrary servers; document that limitation rather than bypassing policy.

## 4. Capture a controlled query

Replace `IFACE` and start:

```bash
sudo tcpdump -ni IFACE -c 10 -w dns-lab.pcap 'port 53'
```

Then query a deliberately unique nonexistent label beneath `example.com`:

```bash
dig handbook-test-invalid.example.com A
```

Stop capture if needed. Expect NXDOMAIN for this reserved documentation domain.

## 5. Inspect Wireshark

Use `dns`. Match query and response using transaction ID, then record query type, flags, response code, answer/authority sections, TTL-related negative caching data, and transport.

## 6. Compare a specific resolver

Only if network policy permits, query an approved resolver explicitly:

```bash
dig @RESOLVER example.com A
```

Do not treat a different CDN answer as automatically wrong; resolver location and steering matter.

## Verification checklist

- [ ] Identified the system resolver path.
- [ ] Compared `getent` and `dig`.
- [ ] Followed delegation roles.
- [ ] Distinguished NOERROR, NXDOMAIN, and SERVFAIL.
- [ ] Matched query/response in a capture.
- [ ] Deleted or sanitized the capture.

## Troubleshooting

- **No port 53 packets:** local caching or encrypted DNS may be in use.
- **`+trace` fails:** direct iterative queries may be blocked.
- **Different answers:** check TTL, resolver, ECS/CDN steering, split DNS, and address family.
- **TCP appears:** response size or truncation may have caused TCP retry.
- **Application differs:** inspect NSS, hosts file, browser/application DNS, proxy, and VPN.

## Cleanup

```bash
rm -f dns-lab.pcap
```

## Reflection questions

1. Which component returned the answer to your client?
2. Was that component authoritative or recursive?
3. Which evidence proves the queried name did not exist?
4. What remains visible when DNS is encrypted?
