---
layout: post
title: "Podman Compose for multi-container setups"
date: 2025-11-22
category: Programming
---

`podman-compose` is the Podman equivalent of `docker-compose`. It reads the same `docker-compose.yml` format and works almost identically. If you have existing Compose files, they usually work without changes.

I use it when I need to run a few services together locally. A typical GIS stack might be a PostGIS database, a GeoServer instance, and a small Python service. Running these manually gets tedious. Compose handles it.

```yaml
# docker-compose.yml
services:
  postgis:
    image: postgis/postgis:15-3.3
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_DB: spatial
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  geoserver:
    image: kartoza/geoserver:2.23.0
    ports:
      - "8080:8080"
    depends_on:
      - postgis

volumes:
  pgdata:
```

```bash
podman-compose up -d       # start everything in the background
podman-compose logs -f     # follow logs
podman-compose down        # stop and remove containers
```

## Install it

`podman-compose` is a separate package, not bundled with Podman.

```bash
pip install podman-compose
```

## One difference from Docker Compose

Networking works slightly differently. In Docker Compose, services can reach each other by service name automatically. In Podman Compose this works too, but only if you're using the default network mode. If you run into connection issues between services, check that they're on the same pod or network.
