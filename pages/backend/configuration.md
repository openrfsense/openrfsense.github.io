---
title: Configuration
layout: default
parent: Backend
permalink: /docs/backend/configuration
---

# Backend configuration
The static configuration for the backend is loaded from a valid YAML file and from environment variables.

## Configuration file
A valid YAML file for the backend (most/all values populated with the defaults) would look like the following:

```yaml
# Backend server configuration
backend:
  # Turn on/off metrics page and API on /metrics
  metrics: true
  # Port on which to serve the API
  port: 8080
  # List of users as 'username: password' pairs for basic auth on the HTTP API endpoints
  users:
    openrfsense: openrfsense

# Collector service configuration
collector:
  # Port on which to wait for TCP packets
  port: 2022

# PostgreSQL database configuration
postgres:
  host: localhost
  port: 5432
  username: postgres
  password: postgres
  dbname: postgres
  # Extra connection parameters, see PostgreSQL documentation
  params:
    - sslmode=disable

# NATS server configuration
nats:
  # Protocol for NATS connection, see NATS docs
  protocol: "tcp"
  # Port for the NATS server
  port: 4222
  # Token for NATS connection encryption
  token: nats-token
```
{: file="config.yml" }

## Environment variables
Any value in the configuration file can be overridden by defining an environment variable with the format `ORFS_SECTION_KEY=value`{: .nowrap }.

Simple strings or integers are simply copied to the configuration object. `ORFS_NATS_TOKEN=custom-token`{: .nowrap } becomes:

```yaml
nats:
  token: "custom-token"
```

Slices/arrays are just strings separated by space. `ORFS_DATABASE_PARAMS="sslmode=disable pooling=true"`{: .nowrap } becomes:

```yaml
database:
  params:
    - "sslmode=disable"
    - "pooling=true"
```
