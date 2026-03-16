---
layout: post
title: "PostGIS is the best GIS tool nobody talks about"
date: 2026-01-25
category: Programming
---

PostGIS is a spatial extension for PostgreSQL. It adds geometry types, spatial indexes, and hundreds of spatial functions to a standard relational database. Most GIS work I do ends up going through it at some point.

The reason it doesn't get talked about as much as QGIS or ArcGIS is that it's not a GUI tool. It's a database. You interact with it through SQL. That's a higher barrier to entry, but the payoff is significant.

## What you can do

Any spatial operation you'd do in a GIS desktop application, you can do in PostGIS with SQL. Clip, buffer, intersect, union, calculate area, find nearest neighbors, reproject. And because it's a database, you can combine these operations with all the standard SQL tools: joins, aggregations, CTEs, window functions.

```sql
-- find all roads within 500m of a flood zone
SELECT r.road_id, r.name
FROM roads r
JOIN flood_zones f
  ON ST_DWithin(r.geom, f.geom, 500)
WHERE f.flood_class = 'high';
```

This kind of query would require multiple manual steps in a desktop GIS. In PostGIS it's one statement.

## Spatial indexing

PostGIS uses GiST indexes for geometry columns. Without an index, spatial queries do a full table scan, which is slow. With an index, they're fast.

```sql
CREATE INDEX roads_geom_idx ON roads USING GIST(geom);
```

Add this after loading data. Most spatial queries will be orders of magnitude faster.

## Getting started

The easiest way locally is Docker or Podman:

```bash
podman run -d \
  --name postgis \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  postgis/postgis:15-3.3
```

Then connect with psql, DBeaver, or QGIS and start loading data. QGIS can read and write PostGIS layers directly, which makes it a good complement to the database.
