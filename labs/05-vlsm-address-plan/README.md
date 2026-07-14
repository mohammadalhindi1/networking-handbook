# Lab 05 — Design a VLSM Address Plan

[← Labs](../README.md) · [Subnetting chapter](../../docs/05-Subnetting/README.md)

## Scenario

You receive `10.42.0.0/22` for a small organization. Design subnets for:

| Segment | Current hosts | Required growth reserve |
|---|---:|---:|
| Engineering | 180 | 20% |
| Student lab | 100 | 20% |
| Servers | 45 | 15 addresses |
| Management | 20 | 10 addresses |
| Guest Wi-Fi | 60 | 25% |
| Router point-to-point link | 2 | none |

## Rules

- Allocate largest requirements first.
- Use the smallest valid subnet that includes the stated reserve.
- Keep allocations inside `10.42.0.0/22`.
- Do not overlap.
- Use the first usable address as gateway except the `/31` link.
- Leave remaining space documented and contiguous where possible.

## Deliverable template

| Segment | Required capacity | CIDR | Network | Gateway | Usable range | Broadcast |
|---|---:|---|---|---|---|---|
| Engineering |  |  |  |  |  |  |

## Workflow

1. Calculate hosts including reserve.
2. Convert each requirement to a prefix.
3. Sort largest to smallest.
4. Allocate from `10.42.0.0` on valid boundaries.
5. Verify containment and overlap.
6. Record free space.

## Verification script

Insert your proposed CIDRs:

```bash
python3 - <<'PY'
import ipaddress

parent = ipaddress.ip_network('10.42.0.0/22')
allocations = {
    # 'engineering': ipaddress.ip_network('10.42.0.0/24'),
}

items = list(allocations.items())
for name, net in items:
    print(f'{name:16} {str(net):18} inside={net.subnet_of(parent)} addresses={net.num_addresses}')

for i, (name_a, net_a) in enumerate(items):
    for name_b, net_b in items[i + 1:]:
        if net_a.overlaps(net_b):
            print(f'OVERLAP: {name_a} and {name_b}')
PY
```

## One valid solution

<details><summary>Reveal after completing your design</summary>

Capacity calculations:

- Engineering: `180 × 1.20 = 216` → `/24` (254 usable).
- Student lab: `100 × 1.20 = 120` → `/25` (126 usable).
- Guest: `60 × 1.25 = 75` → `/25`.
- Servers: `45 + 15 = 60` → `/26` (62 usable).
- Management: `20 + 10 = 30` → `/27` (30 usable).
- Point-to-point: `/31`.

| Segment | CIDR | Gateway | Usable range | Broadcast |
|---|---|---|---|---|
| Engineering | `10.42.0.0/24` | `10.42.0.1` | `.1–.254` | `10.42.0.255` |
| Student lab | `10.42.1.0/25` | `10.42.1.1` | `.1–.126` | `10.42.1.127` |
| Guest Wi-Fi | `10.42.1.128/25` | `10.42.1.129` | `.129–.254` | `10.42.1.255` |
| Servers | `10.42.2.0/26` | `10.42.2.1` | `.1–.62` | `10.42.2.63` |
| Management | `10.42.2.64/27` | `10.42.2.65` | `.65–.94` | `10.42.2.95` |
| Point-to-point | `10.42.2.96/31` | n/a | `.96` and `.97` | none |

Free contiguous space begins at `10.42.2.98`, though future allocations must still use valid prefix boundaries. A designer might intentionally place the `/31` elsewhere to preserve cleaner aligned free blocks; VLSM can have multiple correct designs when constraints are met.

</details>

## Reflection questions

1. Why did Guest require `/25` even though 60 hosts fit in `/26`?
2. What would change if the cloud provider reserved five addresses per subnet?
3. Which allocations could be summarized, and would advertising that summary be safe?
4. How would you preserve a full future `/24` rather than fragmenting it?
