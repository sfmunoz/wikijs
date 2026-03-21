# wikijs

[Wiki.js](https://js.wiki/) Helm chart for deploying Wiki.js `2.5.304` to Kubernetes.

## Overview

- Deploys Wiki.js using image `ghcr.io/requarks/wiki:2.5.304`
- Requires a PostgreSQL database
- Runs in [offline/sideload mode](https://docs.requarks.io/install/sideload) — an init container downloads and verifies the localization bundle at startup
- Configuration is stored as a Kubernetes Secret and mounted at `/wiki/config.yml`
- Exposed via a ClusterIP Service and Ingress at `http://wiki.local`
- Sensitive values (DB password) managed with [helm-secrets](https://github.com/jkroepke/helm-secrets) + [sops](https://github.com/getsops/sops) (age encryption)

## Prerequisites

- Helm 3
- [helm-secrets](https://github.com/jkroepke/helm-secrets) plugin
- [sops](https://github.com/getsops/sops) with an age key matching the recipient in `secrets.yaml`
- A PostgreSQL instance reachable at the host configured in `values.yaml`

## Configuration

Default values are in `values.yaml`. The database password must be provided via `secrets.yaml` (sops-encrypted).

| Value | Default | Description |
|---|---|---|
| `wikijs.config.db.type` | `postgres` | Database type |
| `wikijs.config.db.host` | `postgres-rclone-main` | DB hostname |
| `wikijs.config.db.port` | `5432` | DB port |
| `wikijs.config.db.user` | `wikijs` | DB username |
| `wikijs.config.db.pass` | *(secrets.yaml)* | DB password |
| `wikijs.config.db.db` | `wiki` | Database name |
| `wikijs.config.offline` | `true` | Enable sideload/offline mode |
| `podLabels` | `app: wikijs` | Extra labels on pods |

## Usage

**Render templates locally:**
```sh
helm template .
```

**Dry-run with debug:**
```sh
helm install --dry-run --debug --disable-openapi-validation \
  -f secrets://secrets.yaml -n <namespace> <name> .
```

**Install / upgrade:**
```sh
helm upgrade --install -n <namespace> --create-namespace \
  -f secrets://secrets.yaml <name> .
```

**Uninstall:**
```sh
helm uninstall -n <namespace> <name>
kubectl delete namespaces <namespace>
```

## Releasing

The chart is published as an OCI artifact to GHCR. Push a `v*` tag to trigger the release workflow:

```sh
git tag v1.0.0
git push origin v1.0.0
```

The workflow (`.github/workflows/release.yaml`) packages the chart and pushes it to:

```
oci://ghcr.io/sfmunoz/wikijs
```

**Install from registry:**
```sh
helm install -n <namespace> --create-namespace \
  -f secrets://secrets.yaml <name> \
  oci://ghcr.io/sfmunoz/wikijs --version <version>
```
