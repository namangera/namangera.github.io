---
layout: post
title: "Finding unmapped culverts with DEMs and road data"
date: 2026-03-10
category: Project Writeup
---

Municipal drainage inventories are almost always incomplete. Culverts the pipes and box structures that carry streams under roads are frequently missing from official datasets. This matters for flood modelling, infrastructure assessment, and emergency planning.

The approach is conceptually simple: a culvert exists at almost every point where a stream crosses a road. Find all those intersections, then validate them.

## The intersection step

```python
import geopandas as gpd

roads = gpd.read_file("roads.shp")
streams = gpd.read_file("flowlines.shp")

streams = streams.to_crs(roads.crs)

intersections = gpd.overlay(roads, streams, how='intersection')
candidates = intersections[
    intersections.geometry.geom_type == 'Point'
]
```

For a mid-sized county this usually produces thousands of candidate locations. Most are real culverts. Some are bridges. A small number are errors in the source data.

## Validation with DEMs

A culvert sits at or below road grade the DEM profile across the road at that point will show a depression or flat crossing. A bridge shows a gap or discontinuity. I extract a short elevation profile perpendicular to the road at each candidate point and look at the slope and shape. Not a perfect classifier but it gets you a long way.

## What I learned

Data quality is the real problem. Road centrelines from different sources have different positional accuracy. NHD flowlines in some areas are clearly wrong. I ended up spending more time on data cleaning than on the detection logic itself. That's usually how it goes.
