---
title: Deployment
layout: default
parent: Backend
permalink: /docs/backend/deployment
---

# Backend deployment
This page elaborates on ways of deploying [the backend]({{ site.orfs.backend_repo }}) in different scenarios. Most examples are complete or partial [Docker Compose files](https://docs.docker.com/compose/compose-file/).

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

For the purposes of this example, the directory tree will look something like this:

```
📂 caddy                # Caddy-specific files
  📂 config             # Caddy internal, persistent configuration
  📂 data               # Caddy internal data
  📄 Caddyfile          # Caddy runtime configuration
  📄 Dockerfile         # Builder and runner container spec
📄 docker-compose.yml   # Software stack spec
```
{: no-header="" }

### Setting up Caddy

Caddy does not (yet) proxy layer 4 packets such as TCP. For this reason, the [layer4 extension](https://github.com/RussellLuo/caddy-ext/tree/master/layer4) is required. You can build a Docker image with it using the following Dockerfile.
{: .important .mt-4 }

```dockerfile
FROM caddy:2-builder-alpine AS builder
RUN xcaddy build --with github.com/RussellLuo/caddy-ext/layer4

FROM caddy:2-alpine
COPY --from=builder /usr/bin/caddy /usr/bin/caddy
```
{: file="caddy/Dockerfile" }

A Caddyfile for a fully functional backend proxy could look like this:

```conf
{
    layer4 {
        :2222 {
            proxy orfs_backend:2222
        }
    }
}

orfs.{$DOMAIN} {
    tls {$EMAIL}

    reverse_proxy orfs_backend:8080 {
        header_up X-Real-IP {remote_host}
    }
}
```
{: file="caddy/Caddyfile" }

Note that:
- the subdomain was chosen arbitrarily and the backend doesn't need to be on a specific address or domain
- `{$DOMAIN}` and `{$EMAIL}` will be replaced by environment variables defined in the Docker Compose file
- the `layer4` extension will route all packets received on `$DOMAIN:2222` straight to the backend container

### Creating the full service stack

Plugging Caddy into the existing container stack is quite trivial:

```yaml
services:
  caddy:
    build: ./caddy
    image: caddy-l4
    container_name: caddy
    restart: always
    ports:
      - 80:80        # HTTP
      - 443:443      # TLS
      - 2222:2222    # Measurement collector
    environment:
      DOMAIN: <your domain>
      EMAIL: <your email>
    volumes:
      - ./caddy/Caddyfile:/etc/caddy/Caddyfile:ro
      - ./caddy/data:/data
      - ./caddy/config:/config

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