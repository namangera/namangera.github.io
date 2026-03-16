---
layout: post
title: "What FME is and when it makes sense"
date: 2025-10-12
category: Programming
---

FME (Feature Manipulation Engine) is a data integration platform built specifically for spatial data. You build workflows visually by connecting transformers on a canvas. It can read and write hundreds of formats, and it handles coordinate system conversions, geometry operations, and attribute manipulation without writing code.

I have a complicated relationship with it.

## When it's the right tool

FME is genuinely good when the problem is format conversion or ETL between two systems that don't speak the same language. Say you need to take a set of Shapefiles from a municipality, reproject them, join them with a CSV, validate the geometry, and load them into a PostGIS database. In FME this is a 20-minute job. In Python it's an hour and you'll hit edge cases you didn't expect.

It's also good for non-programmers who need to run repeatable spatial workflows. The visual interface makes it possible to build and hand off a workflow without writing a line of code.

## When it isn't

FME is expensive. The desktop license is a few thousand dollars a year, and the server version for automated workflows is much more. For a solo developer or a small team that's a real cost.

It also creates vendor lock-in. FME workspaces are a proprietary format. If you build your entire pipeline in FME, you're committed to it.

For anything complex or custom, Python with GeoPandas ends up being more flexible and easier to debug. FME workspaces can get unwieldy once they pass a certain size.

## My current position

I use it when a client already has a license and needs something delivered fast. I don't reach for it on my own projects. The Python ecosystem covers most of what I need, and I'd rather have code I can put in version control.
