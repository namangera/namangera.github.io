---
layout: post
title: "GDAL is terrible and I use it every day"
date: 2026-01-10
category: Programming
---

GDAL is the foundational library for reading and writing geospatial raster and vector data. It supports something like 200 formats. It is also one of the most frustrating pieces of software I regularly use.

The API is old. The Python bindings feel like a thin wrapper over a C library, because they are. Error messages are cryptic. The documentation is technically complete but practically unhelpful. Installation has ruined many afternoons.

And yet — I use it constantly, because there is nothing else. QGIS uses it. Rasterio wraps it. You can't really escape it.

## Rasterio makes it bearable

Rasterio is a Python library that wraps GDAL with a much nicer interface. I almost never call GDAL directly in Python anymore.

```python
import rasterio
import numpy as np

with rasterio.open("dem.tif") as src:
    data = src.read(1)
    transform = src.transform
    crs = src.crs

mean_elevation = np.mean(data[data != src.nodata])
```

The underlying GDAL version of the same thing is not readable. Rasterio is.

## The command line tools are actually great

What I've come to appreciate is the GDAL command line utilities. `gdalinfo`, `gdal_translate`, `gdalwarp`, `ogr2ogr` — these are genuinely good for quick format conversion and inspection.

```bash
gdalwarp -t_srs EPSG:4326 input.tif output_wgs84.tif
```

One line to reproject a raster. Hard to argue with that.

I've made peace with GDAL. Just don't try to install it from source on a Friday afternoon.
