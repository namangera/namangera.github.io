---
layout: post
title: "The FME transformers I use most"
date: 2025-11-08
category: Programming
---

FME has over 500 transformers. Most of them are rarely needed. Here are the ones I reach for constantly.

## AttributeManager

Does what it sounds like. Rename, remove, or set attribute values. It replaced a handful of older transformers (AttributeRenamer, AttributeRemover, etc.) and is now the standard way to manage attributes. I use it in almost every workspace.

## Reprojector

Converts between coordinate systems. Usually the first transformer after a reader when the source data isn't in the CRS I need. FME handles the transformation correctly including datum shifts, which is one less thing to worry about.

## Clipper

Clips features to a boundary polygon. The GIS equivalent of a cookie cutter. The "Inside" output gives you features that fall within the boundary, "Outside" gives you the rest. Straightforward and reliable.

## SpatialFilter

More flexible than Clipper. Tests spatial relationships (intersects, contains, within, etc.) between two sets of features and routes them accordingly. Useful when you need to do something based on spatial relationship without actually clipping the geometry.

## FeatureMerger

Joins attributes from one feature set to another based on a matching key. The equivalent of a table join. Most useful when you need to enrich spatial features with attribute data from a non-spatial source.

## GeometryValidator

Checks for invalid geometry: self-intersections, duplicate vertices, unclosed rings. I run this before any analysis that depends on clean geometry, and before loading into a database that enforces geometry validity. Saves time debugging downstream errors.

## StatisticsCalculator

Calculates summary statistics (count, sum, mean, min, max) for a group of features. Useful at the end of a workflow when you need aggregate results rather than individual features.

---

Learning these seven well covers most of what FME is used for day-to-day. The other 490-odd transformers exist for specific situations. Look them up when you need them.
