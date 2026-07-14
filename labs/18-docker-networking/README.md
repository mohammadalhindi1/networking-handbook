# Lab 18 — Inspect a Docker Network

[← Labs](../README.md) · [Docker Networking](../../docs/26-Docker-Networking/README.md)

## Objective

Create an isolated bridge, verify service-name DNS, compare internal and published ports, and inspect the network.

## Run

```bash
docker network create handbook-net
docker run -d --name handbook-web --network handbook-net \
  -p 127.0.0.1:8080:80 nginx:alpine
docker run --rm --network handbook-net alpine \
  sh -c 'wget -qO- http://handbook-web:80 | head'
curl http://127.0.0.1:8080/
```

## Inspect

```bash
docker network inspect handbook-net
docker inspect handbook-web
docker exec handbook-web ip address
docker exec handbook-web ip route
docker port handbook-web
```

## Failure experiment

Attempt `curl http://handbook-web:80` from the host. The service name is scoped to Docker DNS and normally does not resolve on the host. Explain why the container client succeeds.

## Verification

- Container-to-container request used `handbook-web:80`.
- Host request used `127.0.0.1:8080`.
- Published port is loopback-only.
- Network inspection identifies container address/gateway.

## Cleanup

```bash
docker rm -f handbook-web
docker network rm handbook-net
```

## Reflection questions

1. Which port is used inside versus outside?
2. Why is the container IP not a good application configuration value?
3. Which additional controls apply on a cloud VM?
