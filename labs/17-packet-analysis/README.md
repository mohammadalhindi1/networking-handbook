# Lab 17 — Evidence-Driven Packet Analysis

[← Labs](../README.md) · [Wireshark](../../docs/19-Wireshark/README.md) · [Troubleshooting](../../docs/21-Troubleshooting/README.md)

## Objective

Capture one controlled HTTPS request, create a timeline, and write conclusions that distinguish observation from inference.

## Safety

Use `example.com`, a short capture, and no credentials. Delete any capture containing unrelated traffic.

## Capture

```bash
ip route get 1.1.1.1
sudo tcpdump -ni IFACE -s 0 -c 60 -w evidence.pcap 'port 53 or port 443'
```

In another terminal:

```bash
curl -v --connect-timeout 5 https://example.com/ -o /dev/null
```

## Analyze

Use `dns or tcp.port == 443 or udp.port == 443`. Record:

1. DNS query/answer and TTL.
2. Selected address and protocol (TCP or QUIC).
3. Handshake timing.
4. TLS metadata and verification result from `curl`.
5. Encrypted data direction/volume.
6. Closure behavior and retransmissions/resets.

## Evidence table

| Time | Observation | Inference | Confidence/next test |
|---|---|---|---|
| 0.000 | Example | Keep facts separate | Validate elsewhere |

## Verification

- Capture point/interface documented.
- Numeric endpoints and stream identified.
- No claim exceeds what the capture proves.
- Sensitive/unrelated traffic absent.
- Capture deleted or stored securely.

## Cleanup

```bash
rm -f evidence.pcap
```

## Reflection questions

1. What could not be learned from this single capture point?
2. Which delay belonged to network handshake versus server/application?
3. How would NAT or a proxy change correlation?
