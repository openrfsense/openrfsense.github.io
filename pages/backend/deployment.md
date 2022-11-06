---
title: Deployment
layout: default
parent: Backend
permalink: /docs/backend/deployment
---

# Backend deployment
This page contains ways of deploying [the backend]({{ site.orfs.backend_repo }}) in different scenarios. Most examples are complete or partial [Docker Compose files](https://docs.docker.com/compose/compose-file/).

## Docker Compose with port forwarding
This is the same snippet as the one in the Getting Started section.

```yaml
services:
  postgresql:
    image: postgres:alpine
    ports:
      - 5432:5432
    environment:
      POSTGRES_PASSWORD: postgres
  
  orfs_backend:
    image: openrfsense/backend:latest
    ports:
      - 2222:2222     # Measurement collector port
      - 8080:8080     # Backend API/UI HTTP port
    environment:
      ORFS_NATS_TOKEN: nats-token
```

## Docker Compose with reverse proxy (Caddy)
[Caddy](https://caddyserver.com/) is an all-in-one web server and reverse proxy with a [very accessible configuration format](https://caddyserver.com/docs/caddyfile). It can be useful to proxy requests (both HTTP and TCP) to the OpenRF backend.

```conf
# Caddyfile

orfs.{$DOMAIN} {
    tls {$EMAIL}

    reverse_proxy orfs_backend:8080 {
        header_up X-Real-IP {remote_host}
    }
}
```
{: file="Caddyfile"}

```yaml
services:
  postgresql:
    image: postgres:alpine
    ports:
      - 5432:5432
    environment:
      POSTGRES_PASSWORD: postgres
  
  orfs_backend:
    image: openrfsense/backend:latest
    ports:
      - 2222:2222     # Measurement collector port
      - 8080:8080     # Backend API/UI HTTP port
    environment:
      ORFS_NATS_TOKEN: nats-token
```
{: file="docker-compose.yml"}