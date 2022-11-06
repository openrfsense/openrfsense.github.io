---
title: Backend
layout: default
has_children: true
permalink: /docs/backend
---

# Backend

The backend is responsible for:

- controlling and querying the sensors remotely via NATS
- managing encryption keys and TLS certificates for secure communication with the sensors
- exposing a RESTful API to fetch data and provide an abstraction layer over the sensors control
