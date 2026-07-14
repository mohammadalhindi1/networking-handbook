# Lab 19 — Trace a Kubernetes Service

[← Labs](../README.md) · [Kubernetes Networking](../../docs/27-Kubernetes-Networking/README.md)

## Objective

Deploy a local test service, verify DNS, Service port, endpoints, target port, and cleanup. Use an authorized disposable cluster.

## Deploy

```bash
kubectl create namespace handbook-lab
kubectl -n handbook-lab create deployment web --image=nginx:alpine
kubectl -n handbook-lab expose deployment web --port=80 --target-port=80
kubectl -n handbook-lab rollout status deployment/web
```

## Inspect

```bash
kubectl -n handbook-lab get pods -o wide
kubectl -n handbook-lab get svc web -o wide
kubectl -n handbook-lab get endpointslice -l kubernetes.io/service-name=web
kubectl -n handbook-lab run client --rm -it --restart=Never --image=busybox:1.36 -- \
  sh -c 'nslookup web && wget -qO- http://web:80 | head'
```

## Failure experiment

Edit the Service target port to `8080`, observe endpoints/connection behavior, then restore `80`. Do not do this in shared/production namespaces.

## Cleanup

```bash
kubectl delete namespace handbook-lab
```

## Reflection questions

1. Which DNS name/address was returned?
2. Which Pod addresses appeared in EndpointSlice?
3. Why did the wrong target port fail despite correct DNS?
