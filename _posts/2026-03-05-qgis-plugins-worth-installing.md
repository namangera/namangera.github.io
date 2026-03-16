---
layout: post
title: "QGIS plugins worth installing"
date: 2026-03-05
category: Programming
---

QGIS has a large plugin ecosystem. Most of it is mediocre. These are the ones I actually keep installed.

## QuickMapServices

Adds basemap layers (OpenStreetMap, Google, Esri, Bing) with one click. Useful for visual reference and sanity-checking that your data is in the right place. Install it, go to Web > QuickMapServices > Settings > More services > Get contributed pack to unlock the full list.

## QGIS2Web

Exports your QGIS map to a self-contained web map using Leaflet or OpenLayers. The output is HTML, CSS, and JavaScript you can deploy anywhere. It's not perfect for complex maps but it's a quick way to share a map without setting up a server.

## Profile Tool

Generates elevation profiles along a line. You draw a line on the map, it samples a raster (usually a DEM) along that line and plots the elevation profile. I use this constantly for validating DEM data and checking terrain at specific locations.

## Geometry Checker

Runs a suite of geometry validity checks on a vector layer and reports errors. Useful before loading data into PostGIS or running analysis that assumes valid geometry. More thorough than the built-in validity check.

## Points2One

Connects a set of points into a line based on an ordering attribute. Handy when you have GPS track points or ordered survey points that you want to convert to a line geometry.

## QField Sync

Prepares QGIS projects for use with QField, the mobile field data collection app. If you do any field data collection, QField is worth knowing about. Sync prepares the project for offline use on a tablet or phone and handles syncing changes back.

---

Install all plugins through the Plugin Manager: Plugins > Manage and Install Plugins. Search by name. Most install in a few seconds.
