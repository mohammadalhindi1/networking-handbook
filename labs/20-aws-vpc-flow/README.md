# Lab 20 — Trace an AWS VPC Flow

[← Labs](../README.md) · [AWS Networking](../../docs/28-AWS-Networking/README.md)

## Objective

Analyze a proposed flow through AWS without creating resources: client → public ALB → private EC2 target → outbound dependency.

## Scenario

- VPC: `10.20.0.0/16`
- Public subnets: `10.20.0.0/24`, `10.20.1.0/24`
- Private app subnets: `10.20.10.0/24`, `10.20.11.0/24`
- ALB listener: HTTPS 443
- Target: app TCP 8080
- Private default route: NAT gateway

## Trace worksheet

For each leg document source/destination, protocol/port, route table match/target, SG rules, NACL directions, DNS, translation, listener/health check, and return path.

1. Internet client → ALB:443.
2. ALB nodes → app:8080.
3. App → external dependency:443 through NAT.
4. App → S3 through an optional gateway endpoint.

## Failure injections

Explain evidence and fix for:

- ALB SG allows 443 but app SG does not allow 8080 from ALB SG.
- Health path returns 404.
- Private route points to a NAT gateway in failed/misrouted path.
- NACL permits destination 443 but blocks ephemeral return ports.
- VPC CIDR overlaps on-premises VPN prefix.

## Evidence plan

Use EC2 `ip route`, `ss`, `curl`; target health; route tables; SG/NACL; Reachability Analyzer; VPC Flow Logs; ALB logs; NAT metrics; CloudTrail/IaC diff.

## Verification checklist

- Two ALB connections treated separately.
- Both directions and ephemeral ports considered.
- Longest-prefix route selection recorded.
- SG statefulness and NACL statelessness distinguished.
- No resource creation or cost incurred by this documentation lab.

## Reflection questions

1. Which source address does the target see?
2. Why can target health fail when EC2 itself is running?
3. When is an endpoint better than NAT?
4. What does Flow Log `REJECT` isolate, and what remains unknown?
