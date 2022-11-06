---
title: Home
layout: home
nav_order: 1
permalink: /
description: OpenRFSense is an experimental software suite to explore radio frequency data using remote devices and a centralized server.
---

# Anyone can explore the RF spectrum
{: .fs-9 .mb-5 }

OpenRFSense is an experimental software suite to explore radio frequency data using remote devices and a centralized server.
{: .fs-6 .fw-300 }

[Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 }
[View the source code](https://github.com/openrfsense){: .btn .fs-5 .mb-4 .mb-md-0 }

---

The OpenRF project is a suite of free-as-in-freedom software which uses a central control and reporting server to communicate with an arbitrary fleet of remote sensors. Each sensor (or "node", as it may be referred to) is equipped with a [Software-defined Radio](https://en.wikipedia.org/wiki/Software-defined_radio) (or SDR for short), such as a cheap [RTL-SDR](https://www.rtl-sdr.com/).

This project is powered by the following technologies:

<div class="d-flex" style="align-items: center; justify-content: space-around;" markdown="1" >

[![Golang]](https://go.dev/)

[![Docker]](https://docs.docker.com/get-started/overview/)

[![NATS]](https://nats.io/)

[![PostgreSQL]](https://www.postgresql.org/)

</div>

## Getting started
Getting OpenRF up and running can be really easy, thanks to Docker. This guide will show examples and code snippets using fragments of Docker Compose configuration (`docker-compose.yml`) because of its relative ease of understanding and concise representation of containers and resources.

The backend has to be deployed first, but it requires a running PostgreSQL instance to store the radio measurements received from the sensors.

### PostgreSQL
There are no particular requirements regarding the database structure, since the backend will automatically [migrate](https://gorm.io/docs/migration.html) the database table schemas it needs. The database can even be just anything compatible with the PostgreSQL wire protocol, like [CockroachDB](https://www.cockroachlabs.com/product/).

Following the official PostgreSQL Docker documentation ([found here](https://github.com/docker-library/docs/blob/master/postgres/README.md#how-to-use-this-image)), an instance can be deployed like this:

```yaml
postgresql:
  image: postgres:alpine
  ports:
    - 5432:5432
  environment:
    POSTGRES_PASSWORD: postgres
```

This will run PostgreSQL on port `5432` on the host, with username `postgres` and password `postgres`

### Backend
The backend is also trivially simple to deploy, thanks to it being developed with containerization in mind. Based on the PostgreSQL instance used above, a snippet for the backend could look like this:

```yaml
orfs_backend:
  image: openrfsense/backend:latest
  ports:
    - 2222:2222     # Measurement collector port
    - 8080:8080     # Backend API/UI HTTP port
  environment:
    ORFS_NATS_TOKEN: nats-token
```

This example uses the least configuration possible for the backend, as the default configuration baked in the binary:
- expects the database to be reachable on `localhost:5432` (with username `postgres` and password `postgres`), which is the default on PostgreSQL too
- listens for raw TCP packets coming from the sensors on port `2222` (forwarded to the host)
- sends HTTP reponses on port `8080` (also forwarded to the host)

### Complete example
Joining together the two snippets above, a `docker-compose.yml` would look something like the following:

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

The service stack can then be started with Docker Compose in the usual way:

```terminal
$ docker compose up
```

You should then be able to open [`localhost:8080`](http://localhost:8080/) on your machine to see the sensor management UI.

### Node
Deploying and maintaining a sensor/node is also really easy, but it can be done in two main ways.

##### **Manual deployment on a generic machine**
Since all that's needed for the node to run are the binary executable and the configuration file, you can just download [the latest release](https://github.com/openrfsense/node/releases) and run it:

```terminal
$ ./orfs-node --config <your config file>
```

##### **Disk image on a supported board**
Since this method warrants its own page, see [node/deployment](/node/deployment).

### What's next?
If you wish to explore the rest of documentation (highly recommended) or get a better understanding of how the whole system works, visit the following pages:

- Backend
  - In-depth [configuration documentation](/backend/configuration)
  - More [deployment options](/backend/deployment)
- Node
  - In-depth [configuration documentation](/node/configuration)
  - More [deployment options](/node/deployment)


[Golang]: /assets/logos/go.svg
{: width="144px"}

[Docker]: /assets/logos/docker.png
{: width="90px"}

[NATS]: /assets/logos/nats.png
{: width="144px"}

[PostgreSQL]: /assets/logos/postgresql.png
{: width="144px"}