---
title: Backend
layout: default
has_children: true
permalink: /docs/backend
---

# Backend

A self-hostable control center which manages messaging and data collection, wrapped in a RESTful API.
{: .fs-6 .fw-300 }

---

The backend is responsible for:
- controlling and querying the sensors remotely via [NATS](https://nats.io/) (a server is embedded in the backend)
- exposing a RESTful API to fetch data and provide an abstraction layer over the sensors control
- managing encryption keys and TLS certificates for secure communication with the sensors
- providing a simple but functional UI to manage the connected nodes
