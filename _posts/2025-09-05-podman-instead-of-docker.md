---
layout: post
title: "Podman instead of Docker"
date: 2025-09-05
category: Programming
---

I switched to Podman after Docker Desktop changed its licensing terms for commercial use. Turned out to be a good switch regardless.

The main difference is that Podman is daemonless. Docker runs a background daemon that manages everything. Podman runs containers directly as child processes of your shell. In practice this mostly doesn't matter, but it means no daemon to restart when things go wrong, and no single process with root privileges sitting in the background all the time.

The CLI is a drop-in replacement. Every Docker command I knew works with Podman.

```bash
# these work exactly the same
podman pull ubuntu:22.04
podman run -it ubuntu:22.04 bash
podman build -t myimage .
podman ps
```

If you have scripts that use `docker`, you can alias it:

```bash
alias docker=podman
```

## What I actually use it for

Running geospatial tools that are annoying to install. GDAL, PDAL, and their dependencies can be painful to get working on a Mac or Windows machine. Running them in a container sidesteps the whole problem.

```bash
podman run --rm -v $(pwd):/data ghcr.io/osgeo/gdal:ubuntu-full-latest \
  gdalinfo /data/dem.tif
```

Mount your local directory, run the command, get the output. No installation required.

## One gotcha

Podman on Mac runs containers inside a Linux VM (same as Docker Desktop). You start it with `podman machine init` and `podman machine start`. The first time this takes a few minutes. After that it's fast.
