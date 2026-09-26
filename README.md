# wikijs

Helm chart for Wiki.js `2.5.304` on Kubernetes.

## What it deploys

- Wiki.js from `ghcr.io/requarks/wiki:2.5.304`
- A ClusterIP Service and Gateway API `HTTPRoute`
- A Secret-mounted `/wiki/config.yml`
- A `128Mi` `local-path` PVC for the downloaded sideload localization bundle

Wiki.js requires a reachable PostgreSQL database. The chart runs Wiki.js in
[sideload mode](https://docs.requarks.io/install/sideload); its init container
downloads and verifies the localization bundle at startup.

## Prerequisites

- Helm 3
- A Gateway API implementation with a parent Gateway matching `route.parent`
- A PostgreSQL instance reachable from the cluster
- A `local-path` storage class
- [helm-secrets](https://github.com/jkroepke/helm-secrets), [sops](https://github.com/getsops/sops), and an age key for the encrypted `secrets.yaml`

## Configuration

Defaults are in `values.yaml`. Put `wikijs.config.db.pass` in the local,
sops-encrypted `secrets.yaml`.

| Value | Default | Description |
|---|---|---|
| `wikijs.config.db.type` | `postgres` | Database type |
| `wikijs.config.db.host` | `postgres-rclone-main` | Database host |
| `wikijs.config.db.port` | `5432` | Database port |
| `wikijs.config.db.user` | `wikijs` | Database user |
| `wikijs.config.db.pass` | `secrets.yaml` | Database password |
| `wikijs.config.db.db` | `wiki` | Database name |
| `wikijs.config.offline` | `true` | Enable sideload mode |
| `route.hostname` | `wiki.local` | HTTPRoute hostname |
| `route.parent.name` | `https-8443` | Parent Gateway name |
| `route.parent.namespace` | `kube-system` | Parent Gateway namespace |
| `route.parent.sectionName` | `https` | Parent Gateway listener section |
| `podLabels` | `app: wikijs` | Additional Pod labels |

## Usage

```sh
helm lint .
helm template .

helm upgrade --install -n <namespace> --create-namespace \
  -f secrets://secrets.yaml <name> .

helm uninstall -n <namespace> <name>
kubectl delete namespace <namespace>
```

## Release

Pushing a `v*` tag publishes the chart to GHCR. The tag without its `v` prefix
becomes the chart version.

```sh
git tag v0.2.3
git push origin v0.2.3
```

Install a published version:

```sh
helm install -n <namespace> --create-namespace \
  -f secrets://secrets.yaml <name> \
  oci://ghcr.io/sfmunoz/wikijs --version <version>
```

## Contributing

See [AGENTS.md](AGENTS.md) for repository conventions.
