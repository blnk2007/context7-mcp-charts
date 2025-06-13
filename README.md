# Context7 MCP Deployment

This repository provides a reproducible, version-pinned deployment of the [Context7 MCP server](https://github.com/upstash/context7) for Kubernetes, using Docker and Helm. The Helm chart is published via a public charts-only repository.

---

## Repository Structure

- `charts/context7-mcp/` – The Helm chart for deploying Context7 MCP (see its [README](charts/context7-mcp/README.md) for full chart details)
- `dockerfile/` – Dockerfile for building a custom MCP image
- `memory-bank/` – Project documentation, architecture, and changelog (if you require privacy, move this folder to a private repo or encrypt it)
- `.github/workflows/helm-release.yml` – GitHub Actions workflow to publish the chart to the public charts repo
- `.github/workflows/docker-release.yml` – GitHub Actions workflow to build and push the Docker image to private GHCR

---

## Quickstart

### 1. Build the Docker Image (optional)

If you want to build your own image (default chart uses the official image):

```bash
docker build -t context7-mcp:$(yq .context7McpVersion charts/context7-mcp/values.yaml) dockerfile/
```

### 2. Add the Public Helm Chart Repository

The chart is published at:

**Helm CLI:**
```bash
helm repo add context7 https://blnk2007.github.io/context7-mcp-charts
helm repo update
helm search repo context7
```

**Rancher:**
- Go to **Apps & Marketplace → Repositories → Create**
- **Name:** context7
- **Index URL:** `https://blnk2007.github.io/context7-mcp-charts`
- Click **Create**. The chart will appear in the Rancher UI.

### 3. Install the Chart

```bash
helm install context7 context7/context7-mcp
# Or specify a version:
helm install context7 context7/context7-mcp --version 0.1.0
```

### 4. Upgrade the Chart

```bash
helm upgrade context7 context7/context7-mcp --set context7McpVersion=<new-version>
```

---

## Private Docker Image & Cluster Setup

- The chart pulls the image from `ghcr.io/blnk2007/context7-mcp` (private).
- You must create a Kubernetes image pull secret named `ghcr-cred`:
  ```bash
  kubectl create secret docker-registry ghcr-cred \
    --docker-server=ghcr.io \
    --docker-username=blnk2007 \
    --docker-password=<your CR_PAT> \
    --namespace default
  ```
- The chart's `values.yaml` includes:
  ```yaml
  imagePullSecrets:
    - name: ghcr-cred
  ```

---

## Contribution & Commit Guidelines

- All version bumps must reference the MCP version in the commit message.
- Memory Bank documentation must be updated and committed for all significant changes.
- Chart and image changes are automatically published via workflow.

---

## More Information

- See [charts/context7-mcp/README.md](charts/context7-mcp/README.md) for full chart configuration and usage.
- See [memory-bank/](memory-bank/) for project architecture, changelog, and technical context.  
  If you require privacy, move this folder to a private repo or encrypt it.

---

## License

See [Context7 MCP upstream license](https://github.com/upstash/context7#license).
