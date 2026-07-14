# IPv4 Subnetting Quick Reference

[Home](../README.md) · [Subnetting chapter](../docs/05-Subnetting/README.md) · [VLSM lab](../labs/05-vlsm-address-plan/README.md)

## Core formulas

```text
Host bits                    = 32 − prefix length
Addresses per subnet         = 2^(host bits)
Traditional usable hosts     = 2^(host bits) − 2
Number of borrowed subnets   = 2^(borrowed bits)
```

Exceptions: `/31` is useful on point-to-point links and `/32` represents one address/host route. Cloud providers may reserve additional addresses.

## Common prefixes

| CIDR | Mask | Addresses | Traditional usable | Block size in changing octet |
|---:|---|---:|---:|---:|
| `/16` | `255.255.0.0` | 65,536 | 65,534 | 1 in 2nd/3rd boundary context |
| `/20` | `255.255.240.0` | 4,096 | 4,094 | 16 in 3rd octet |
| `/21` | `255.255.248.0` | 2,048 | 2,046 | 8 in 3rd octet |
| `/22` | `255.255.252.0` | 1,024 | 1,022 | 4 in 3rd octet |
| `/23` | `255.255.254.0` | 512 | 510 | 2 in 3rd octet |
| `/24` | `255.255.255.0` | 256 | 254 | 1 in 3rd octet |
| `/25` | `255.255.255.128` | 128 | 126 | 128 in 4th octet |
| `/26` | `255.255.255.192` | 64 | 62 | 64 |
| `/27` | `255.255.255.224` | 32 | 30 | 32 |
| `/28` | `255.255.255.240` | 16 | 14 | 16 |
| `/29` | `255.255.255.248` | 8 | 6 | 8 |
| `/30` | `255.255.255.252` | 4 | 2 | 4 |
| `/31` | `255.255.255.254` | 2 | 2 on point-to-point | 2 |
| `/32` | `255.255.255.255` | 1 | 1 host route | 1 |

## Fast block-size method

1. Find the first mask octet that is not `255` or `0`.
2. Calculate `256 − mask octet`.
3. Subnet boundaries are multiples of that block size.

Example: `/27` → mask `255.255.255.224` → `256 − 224 = 32`.

```text
Networks:   .0   .32   .64   .96   .128   .160   .192   .224
Broadcasts: .31  .63   .95   .127  .159   .191   .223   .255
```

## VLSM workflow

1. List required usable hosts from largest to smallest.
2. Add capacity for growth when the design requires it.
3. Choose the smallest fitting prefix for each requirement.
4. Allocate on valid aligned boundaries.
5. Record network, usable range, broadcast, gateway, and purpose.
6. Confirm no overlap and leave intentional free space.

## Verification commands

```bash
ipcalc 192.0.2.77/27
python3 -c 'import ipaddress; print(ipaddress.ip_interface("192.0.2.77/27").network)'
ip route get 198.51.100.20
```

Use calculators to verify reasoning, not replace it.
