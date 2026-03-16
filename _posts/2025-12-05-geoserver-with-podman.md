---
layout: post
title: "Running GeoServer locally with Podman"
date: 2025-12-05
category: Programming
---

GeoServer is an open-source server for sharing geospatial data over standard protocols: WMS, WFS, WCS. It's the standard way to serve vector and raster data to GIS clients, web maps, and other services.

Installing it natively involves Java, a servlet container, and some configuration. Running it in a container is much simpler.

```bash
podman run -d \
  --name geoserver \
  -p 8080:8080 \
  -v $(pwd)/geoserver_data:/opt/geoserver/data_dir \
  kartoza/geoserver:2.23.0
```

After a minute or so, GeoServer is available at `http://localhost:8080/geoserver`. Default credentials are `admin` / `geoserver`.

## Persisting data

The `-v` flag mounts a local directory as GeoServer's data directory. This means your layers, styles, and configuration persist when the container stops. Without it, everything resets each time.

Create the directory first:

```bash
mkdir geoserver_data
```

## Publishing a layer

1. Add a new workspace in the web interface
2. Add a data store pointing to a Shapefile, PostGIS database, or GeoTIFF
3. Publish a layer from that store
4. Preview it with the built-in OpenLayers viewer

The WMS endpoint for that layer is then:

```
http://localhost:8080/geoserver/[workspace]/wms
```

You can add this directly to QGIS as a WMS connection and see the layer in your map.

## For production

The Kartoza image is fine for local development. For production, look at the official GeoServer Docker image and spend time on memory tuning. GeoServer's default heap size is too small for anything beyond toy datasets.
