# Context7 MCP Helm Chart

This Helm chart deploys the [Context7 MCP server](https://github.com/upstash/context7) to Kubernetes, providing a reproducible, configurable, and version-pinned deployment for AI and LLM infrastructure.

---

## Features

- Deploys Context7 MCP as a Kubernetes Deployment and Service
- Version pinning via `context7McpVersion` (single source of truth)
- Configurable resources, environment, and service type (NodePort for LAN access)
- Dockerfile and chart always reference the same MCP version
- Memory Bank documentation included (but not packaged in chart; move to a private repo or encrypt if you require privacy)

---

## Versioning Strategy

- The `context7McpVersion` value in `values.yaml` controls the MCP version everywhere:
  - Docker image tag
  - Helm `appVersion`
  - Dockerfile build ARG
- **Always update `context7McpVersion` and reference the version in commit messages when upgrading.**
- Example commit message:  
  `chore(chart): bump context7-mcp to v0.9.1`

---

## Quickstart

### Add This Chart Repo

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

### Install the Chart

```bash
helm install context7 context7/context7-mcp
# Or specify a version:
helm install context7 context7/context7-mcp --version 0.1.0
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

## Configuration Reference

| Parameter             | Default                | Description                                      |
|-----------------------|------------------------|--------------------------------------------------|
| `context7McpVersion`  | `v0.9.1`               | MCP version for Docker image, chart, Dockerfile  |
| `replicaCount`        | `1`                    | Number of pod replicas                           |
| `image.repository`    | `ghcr.io/blnk2007/context7-mcp` | Docker image repository                |
| `image.tag`           | `{{ .Values.context7McpVersion }}` | Docker image tag (do not change)      |
| `image.pullPolicy`    | `IfNotPresent`         | Image pull policy                                |
| `imagePullSecrets`    | `[ghcr-cred]`          | Image pull secret for private registry           |
| `service.type`        | `NodePort`             | Service type (NodePort for LAN)                  |
| `service.port`        | `8080`                 | Service port                                     |
| `service.nodePort`    | `30080`                | NodePort for external access                     |
| `resources`           | See `values.yaml`      | CPU/memory requests and limits                   |
| `env`                 | `{}`                   | Environment variables for MCP                    |
| `nodeSelector`        | `{}`                   | Node selection constraints                       |
| `tolerations`         | `[]`                   | Tolerations for scheduling                       |
| `affinity`            | `{}`                   | Affinity rules                                   |

---

## Upgrade Notes

- To upgrade the MCP version:
  1. Update `context7McpVersion` in `values.yaml`
  2. Rebuild the Docker image if building locally
  3. Commit with a message referencing the new version
  4. Run `helm upgrade` to apply the new version

---

## Development

- Lint the chart:  
  `helm lint charts/context7-mcp`
- Render templates:  
  `helm template charts/context7-mcp`
- Build Docker image:  
  `docker build -t context7-mcp:$(yq .context7McpVersion charts/context7-mcp/values.yaml) dockerfile/`
- Test deployment on k3s or any Kubernetes cluster

---

## Commit Guidelines

- All version bumps must reference the MCP version in the commit message.
- Memory Bank documentation updates should be committed separately with a `docs(memory-bank): ...` prefix.

---

## License

See [Context7 MCP upstream license](https://github.com/upstash/context7#license).
