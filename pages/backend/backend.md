---
title: Backend
layout: default
has_children: true
permalink: /docs/backend
---

# Backend

The backend is responsible for:
- controlling and querying the sensors remotely via [NATS](https://nats.io/)
- exposing a RESTful API to fetch data and provide an abstraction layer over the sensors control
- managing encryption keys and TLS certificates for secure communication with the sensors
- providing a simple but functional UI to manage the connected nodes
