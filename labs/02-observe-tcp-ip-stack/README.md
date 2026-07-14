# Lab 02 — Observe the TCP/IP Stack

[← Labs](../README.md) · [TCP/IP chapter](../../docs/02-TCP-IP-Model/README.md)

## Objective

Observe one HTTPS request across the TCP/IP layers: local interface and route, DNS resolution, transport connection, TLS, application response, and packet capture.

## Prerequisites

- Linux or WSL.
- `ip`, `dig`, `curl`, `ss`, and `tcpdump`.
- `sudo` permission for packet capture.
- Authorization to capture traffic on the selected interface.

## Safety and privacy

Capture only the training request. Do not log in, submit forms, or capture unrelated traffic. The commands use `example.com`, which is reserved for documentation.

## 1. Identify the route

```bash
ip -brief address
ip route get 93.184.216.34
```

The example address may change or may not be used by your resolver. The purpose is to observe the selected source, interface, and gateway. In later steps, use the address returned in your environment.

## 2. Query DNS

```bash
dig example.com A
dig example.com AAAA
```

Record the resolver, response code, answer addresses, TTL, and query time. An empty AAAA answer is different from a failed DNS query.

## 3. Start a focused capture

Replace `eth0` with your active interface:

```bash
sudo tcpdump -ni eth0 -w tcp-ip-lab.pcap \
  'port 53 or port 443'
```

The capture includes classic DNS and HTTPS transports. Encrypted DNS may not appear as port 53. Stop with `Ctrl+C` immediately after the request.

## 4. Generate the request

In another terminal:

```bash
curl -v --connect-timeout 5 https://example.com/ -o /dev/null
```

Observe:

- hostname resolution and chosen IPv4/IPv6 address;
- connection target and port;
- TLS version, certificate verification, and negotiated application protocol;
- HTTP response status and headers.

Do not use `-k`; the goal includes validating the normal TLS trust path.

## 5. Inspect socket state

Run during or immediately after a request:

```bash
ss -tan '( dport = :443 or sport = :443 )'
```

Depending on timing, you may see `ESTAB`, `FIN-WAIT`, or `TIME-WAIT`. Short-lived connections can disappear before inspection.

## 6. Analyze in Wireshark

Open `tcp-ip-lab.pcap` and use:

```text
dns or tcp.port == 443 or udp.port == 443
```

Find evidence for each layer:

| Layer | Evidence |
|---|---|
| Application | DNS query/answer; TLS handshake; encrypted application data |
| Transport | TCP handshake and ports, or QUIC over UDP |
| Internet | Source/destination IP, TTL/Hop Limit |
| Network Access | Source/destination MAC and EtherType |

If TCP is used, right-click one packet and select **Follow → TCP Stream**. HTTPS content remains encrypted, but conversation endpoints and encrypted record flow remain visible.

## Expected observations

- Link-layer destination is normally the gateway for a remote server.
- IP destination identifies the remote endpoint or intermediary selected by DNS/CDN behavior.
- The client uses an ephemeral source port and destination port 443.
- TLS metadata is partly visible, while HTTP content is encrypted.
- Exact addresses, protocols, and packet counts vary because CDNs, IPv6, QUIC, caches, and resolver configuration differ.

## Verification checklist

- [ ] Identified active interface, source address, and gateway.
- [ ] Recorded DNS response and TTL.
- [ ] Identified TCP or QUIC transport.
- [ ] Located IP and link-layer endpoints.
- [ ] Confirmed TLS verification and HTTP response.
- [ ] Stopped the capture and removed it if it contains unrelated traffic.

## Troubleshooting

- **`dig` missing:** install the DNS utilities package for your distribution.
- **No DNS packets:** the answer may be cached or DNS may be encrypted; flush only if safe or document the observation.
- **No TCP handshake:** the client may use HTTP/3/QUIC; include UDP port 443.
- **Capture file is empty:** verify the active interface; `tcpdump -D` lists capture interfaces.
- **Certificate error:** check system time, hostname, trust store, interception proxy, and certificate details; do not bypass verification as the fix.
- **WSL differences:** WSL traffic crosses a virtual network; capture inside the environment generating the request.

## Cleanup

Inspect the capture before keeping it. Delete it if it contains unrelated or sensitive traffic:

```bash
rm -f tcp-ip-lab.pcap
```

## Reflection questions

1. Which values identify the application process, host route, and local next hop?
2. Which headers changed between links, and which remained end to end?
3. Did the client use IPv4 or IPv6? TCP or QUIC?
4. What could Wireshark observe despite TLS, and what remained protected?
