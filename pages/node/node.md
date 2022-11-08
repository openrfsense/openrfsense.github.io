---
title: Node
layout: default
has_children: true
permalink: /docs/node
---

# Node

An all-in-one sensor management daemon which acts on behalf of the backend and collects useful data.
{: .fs-6 .fw-300 }

---

The node software is designed to run as a daemon on remote/embedded devices, which serve as RF monitoring stations. This daemon is responsible for:
- Handling commands sent by the backend through [NATS](https://nats.io/) and responding accordingly
- Collecting meaningful system metrics and relaying them on command
- Showing a simple web interface exposed on `localhost`, easily accessible via the temporary hotspot (see [`openrfsense/image`](https://github.com/openrfsense/image)) or on the device's own IP