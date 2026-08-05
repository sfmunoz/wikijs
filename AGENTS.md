# AGENTS.md

Guidance for AI coding agents and contributors working in this repository.
For complete project information — prerequisites, configuration values,
installation/usage, and the release process — see [README.md](README.md).

## Project

- Helm chart (v2) deploying [Wiki.js](https://js.wiki/) `2.5.304` to Kubernetes.
- Published as an OCI artifact to `oci://ghcr.io/sfmunoz/wikijs`.
- DB credentials are provided via a local `secrets.yaml` (helm-secrets + sops
  with age encryption). Never commit plaintext secrets.

## Repository layout

- `Chart.yaml` — chart metadata (name, version, appVersion)
- `values.yaml` — default values; the single source for chart configuration
- `templates/` — Kubernetes manifests: `deployment.yaml`, `service.yaml`,
  `ingress.yaml`, `secret.yaml`, `NOTES.txt`, and `_helpers.tpl` (name/label helpers)
- `.github/workflows/release.yaml` — publishes the chart to GHCR on `v*` tag push
- `README.md` — user-facing documentation (see above)

## Verification

Run before committing changes to templates or values:

```sh
helm lint .
helm template .
```

For dry-run, install, and uninstall commands, see [README.md](README.md) (Usage).

## Conventions

- **Commit messages:** `path: description` (e.g. `ingress.yaml: ingress.hostname
  enforced`). No conventional-commit prefixes.
- **Branches:** `<type>/<issue-number>-<slug>` with abbreviated types
  (`enh/...`, `doc/...`).
- **Required values:** `ingress.hostname` is mandatory — enforced via
  `required` in `templates/ingress.yaml`. Keep it defined in `values.yaml`.
- **Config flow:** `values.yaml` → `templates/secret.yaml` (mounted at
  `/wiki/config.yml`). Keep the configuration table in README.md in sync with
  `values.yaml`.
- **Documentation:** keep README.md the single user-facing doc; do not
  duplicate its content here. Add new user-facing content to README.md and
  reference it from here.
