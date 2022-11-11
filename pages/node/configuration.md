---
title: Configuration
layout: default
parent: Node
permalink: /docs/node/configuration
---

# Node configuration

The static configuration for the node is loaded from a valid YAML file and from environment variables.

## Configuration file
A valid YAML file for the node (most/all values populated with the defaults) would look like the following:

```yaml
# Node daemon configuration
node:
  # Port on which to serve the UI
  port: 9090

# Location information (required)
location:
  # Readable name of the location
  name: Trento
  # Altitude of the sensor
  elevation: 200.0
  # Geographic coordinates of the sensor
  latitude: 46.0669256
  longitude: 11.1481102

# Collector service configuration
collector:
  # Collector host (the backend's host)
  host: BACKEND-IP
  # Port on which to wait for TCP packets
  port: 2022

# NATS server configuration
nats:
  # NATS host (generally the same as the backend service)
  host: BACKEND-IP
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


Slices/arrays are just strings separated by space. `ORFS_NATS_PARAMS="param1 param2"`{: .nowrap } becomes:
> ⚠️ Please note that the following isn't (yet) used in the node configuration

```yaml
nats:
  params:
    - "param1"
    - "param2"
```