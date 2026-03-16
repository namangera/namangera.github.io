---
layout: post
title: "Automating FME workspaces with FME Flow"
date: 2026-02-10
category: Programming
---

FME Workbench is for building workflows interactively. FME Flow (formerly FME Server) is for running them automatically on a schedule or triggered by an event. The distinction matters once you have workflows that need to run without someone manually pressing a button.

## How it works

You publish a workspace from Workbench to Flow. Flow can then:

- Run it on a schedule (cron-style)
- Trigger it when a file lands in a folder
- Expose it as a REST API endpoint
- Run it in response to a database change

For GIS data pipelines, the most common patterns are scheduled runs (nightly update of a spatial dataset) and file-triggered runs (process a Shapefile when it's dropped in a folder).

## The REST API option is underused

FME Flow can expose any workspace as an HTTP endpoint. You send a POST request with parameters, it runs the workspace and returns the result. This is a quick way to build a spatial processing API without writing a server.

```bash
curl -X POST \
  https://your-fme-server/fmerest/v3/transformations/submit/MyRepository/MyWorkspace.fmw \
  -H "Authorization: fmetoken token=YOUR_TOKEN" \
  -d '{"publishedParameters": [{"name": "INPUT_FILE", "value": "data.shp"}]}'
```

It's not elegant, but it works and it's fast to set up.

## The cost problem again

FME Flow licensing is separate from FME Workbench and significantly more expensive. For small teams or solo work it's hard to justify. If you just need scheduled runs, a cron job calling the FME command line engine is a reasonable free alternative:

```bash
# run a workspace from the command line
fme workspace.fmw --INPUT_FILE data.shp --OUTPUT_DIR /output
```

This works anywhere FME Desktop is installed and doesn't require Flow.
