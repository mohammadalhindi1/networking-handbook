# Chapter 26 — Docker Networking

[← DevOps Networking](../25-DevOps-Networking/README.md) · [Handbook](../README.md) · [Kubernetes Networking →](../27-Kubernetes-Networking/README.md)

> **Learning objectives**
> - Explain container network namespaces, veth pairs, bridges, routing, DNS, and port publishing.
> - Distinguish bridge, host, none, overlay, macvlan, and ipvlan drivers.
> - Diagnose container-to-container, host, Internet, and published-port failures.

## 1. Introduction

Docker gives containers isolated network namespaces with their own interfaces, routes, neighbor tables, sockets, and resolver configuration. On a user-defined bridge, Docker connects container veth interfaces to a Linux bridge, allocates addresses, provides service-name DNS, and configures forwarding/NAT for external access.

`EXPOSE` documents a container port; it does not publish it. `-p HOST_PORT:CONTAINER_PORT` creates host-side forwarding so external clients can reach the container listener.

## 2. Theory

### Common drivers

| Driver | Behavior | Typical use |
|---|---|---|
| bridge | Isolated network on one Docker host | Local apps and Compose |
| host | Shares host namespace | Performance/special integration; less isolation |
| none | Loopback only | Fully isolated/custom setup |
| overlay | Multi-host logical network in Swarm | Distributed services |
| macvlan | Container appears with its own MAC on physical LAN | Legacy/L2 integration |
| ipvlan | Shares parent MAC with L3/L2 addressing modes | Scale or restricted MAC environments |

User-defined bridges are preferred over the legacy default bridge for application stacks because they provide scoped isolation and embedded DNS by container/service name.

### Namespaces, veth, bridge, and NAT

The container's `eth0` is commonly one side of a veth pair. The host-side peer attaches to a Docker bridge. Containers on the same user-defined bridge communicate directly subject to policy. Outbound traffic is commonly masqueraded through the host. Published ports create firewall/NAT/proxy behavior from host address/port to the container.

### Port publishing

```text
Client → HOST_IP:8080 → Docker forwarding/NAT → CONTAINER_IP:80 → listener
```

Publishing to `127.0.0.1:8080:80` restricts host exposure to loopback. Publishing without a host address can expose on all host interfaces depending on configuration. Always verify actual firewall rules and listeners.

### Docker DNS

Containers on a user-defined network can resolve other containers/services by name. Compose service names are stable discovery names while container IPs can change after recreation. `localhost` inside a container refers to that container, not the host or another service.

### Compose networking

Compose creates a project-scoped default network unless custom networks are defined. Services join by name and communicate using **container ports**, not published host ports. Publishing is needed only for traffic entering from outside the Docker network.

> **Did you know?** A container can reach another Compose service at `db:5432` even when no database port is published to the host.

> **Memory trick:** **Inside uses service name + container port; outside uses host address + published port.**

### Behind the scenes

Docker Engine manages Linux networking and firewall rules; Docker Desktop runs containers inside a VM and adds another host/VM boundary. Exact bridge names, firewall backend, and host-reachability behavior differ by platform and Docker version.

## 3. Visual diagram

```mermaid
flowchart LR
    C["Client :8080"] --> H["Docker host"]
    H --> B["Bridge network"]
    B --> W["web :80"]
    W -->|"db:5432"| D["database"]
```

## 4. Real-world example

A web container listens on `0.0.0.0:80`, is published as `127.0.0.1:8080:80`, and connects to `db:5432` on a private user-defined network. Users reach only the web port locally; the database remains unpublised but reachable by the web service.

### Real industry usage

Docker networks support developer environments, CI services, Compose stacks, local integration tests, and single-host production workloads. Multi-host orchestrators add overlay/routing abstractions.

### Cloud perspective

The Docker host itself lives inside a cloud subnet with security groups/routes. A published port still requires cloud policy and host firewall reachability. Container NAT creates an additional address/observability boundary.

### DevOps perspective

Define networks and ports in Compose, avoid hard-coded IPs, add health checks, test DNS and listeners inside containers, and prevent application CIDRs from overlapping VPN/cloud/Kubernetes networks.

### Cybersecurity perspective

Publish only required ports and bind them narrowly. Separate frontend/backend networks, avoid host networking unless justified, run without unnecessary capabilities, and treat Docker daemon access as highly privileged.

## 5. Packet journey

For external published traffic: host receives frame/IP packet, host policy and Docker DNAT select container address/port, bridge/veth delivers it, container socket accepts, and reverse state restores the host-facing tuple. Container egress follows its default route to the bridge gateway and is commonly SNATed by the host.

## 6. Linux commands

```bash
docker network ls
docker network inspect NETWORK
docker inspect CONTAINER
docker exec CONTAINER ip address
docker exec CONTAINER ip route
docker exec CONTAINER getent hosts SERVICE
docker port CONTAINER
ss -lntup
```

On Linux hosts, correlate with `ip link`, `bridge fdb show`, `nft list ruleset`, and `nsenter -t PID -n` when authorized.

## 7. Practical example

Complete [Lab 18: Inspect a Docker network](../../labs/18-docker-networking/README.md).

## 8. Wireshark example

Capture on host external interface, Docker bridge, and container namespace. Compare pre/post-DNAT tuples. Filters: `tcp.port == 8080`, `tcp.port == 80`, and container IP. Host offload and bridge capture points can show duplicates or different link headers.

## 9. Common mistakes

- Treating `EXPOSE` as port publishing.
- Connecting service-to-service through the host published port.
- Binding application to container `127.0.0.1` and expecting publishing to work.
- Using container IPs as stable discovery.
- Confusing Docker Desktop VM and physical host networking.
- Overlapping Docker network CIDR with VPN/VPC.

## 10. Troubleshooting

| Symptom | Evidence |
|---|---|
| Name does not resolve | network membership, `getent`, Compose service name |
| Connection refused | listener bind/port inside container |
| Container works internally, published port fails | `docker port`, host bind/firewall/NAT/cloud policy |
| Internet fails | container route, host forwarding/NAT/DNS |
| Host reachable but container not | bridge/veth/firewall and correct destination |
| Works Linux, differs Desktop | VM boundary and Desktop networking docs |

### Best practices

- Use user-defined networks and service names.
- Publish only ingress-facing services.
- Bind host ports to required interfaces.
- Keep backend services unpublised.
- Inspect from both host and container namespaces.
- Validate address-pool overlap before deployment.

## 11. Interview questions

### `EXPOSE` versus `-p`?

<details><summary>Answer</summary>`EXPOSE` is image metadata/documentation. `-p` publishes a container port through a host address/port.</details>

### Why does `localhost` fail between containers?

<details><summary>Answer</summary>Each container normally has its own network namespace; localhost refers to itself. Use service DNS and container port.</details>

### Bridge versus host network?

<details><summary>Answer</summary>Bridge provides namespace isolation and forwarding/NAT. Host shares the host network stack, reducing isolation and port namespace separation.</details>

## 12. Quiz

1. Does a Compose service need a published port for another service to reach it? 2. What is the likely issue if the app binds `127.0.0.1`? 3. Which command shows network members/config? 4. What does `-p 8080:80` mean?

<details><summary>Answers</summary>

1. No. 2. It accepts only loopback inside the container. 3. `docker network inspect`. 4. Forward host port 8080 to container port 80.

</details>

## FAQ

### How does container reach host?

Platform-specific. Docker Desktop provides special hostnames; native Linux depends on gateway/routing/configuration. Check current official docs.

### Is container IP public?

Usually it belongs to a private host-local/overlay network and requires routing or publishing for external access.

## 13. Summary

Docker networking combines namespaces, veth, bridges, DNS, routes, firewalling, and NAT. Use service discovery internally, publish only intended ports, and diagnose from both container and host paths.

## References

- [Docker networking overview](https://docs.docker.com/engine/network/)
- [Docker network drivers](https://docs.docker.com/engine/network/drivers/)
- [Port publishing](https://docs.docker.com/engine/network/port-publishing/)
- [Compose networking](https://docs.docker.com/compose/how-tos/networking/)
