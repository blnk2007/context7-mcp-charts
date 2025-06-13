# Context7 MCP Helm Chart

This repository contains only the public Helm chart for deploying [Context7 MCP](https://github.com/upstash/context7) to Kubernetes.

- Chart directory: `charts/context7-mcp/`
- Chart documentation: [charts/context7-mcp/README.md](charts/context7-mcp/README.md)
- Chart repo URL: https://blnk2007.github.io/context7-mcp-charts

## Usage

Add this repo to Helm:

```bash
helm repo add context7 https://blnk2007.github.io/context7-mcp-charts
helm repo update
helm search repo context7
```

Or add to Rancher as a Helm repo using the same URL.

## Publishing

This repo uses GitHub Actions to publish the chart to GitHub Pages (`gh-pages` branch) on every push to `master`.
