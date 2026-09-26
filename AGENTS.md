# AGENTS.md

Contributor instructions. User-facing setup, configuration, commands, and release
information belong in [README.md](README.md); do not duplicate them here.

## Work on the chart

- Chart metadata: `Chart.yaml`; defaults: `values.yaml`; manifests: `templates/`.
- Database credentials belong in a sops-encrypted local `secrets.yaml`. Never
  commit plaintext secrets.
- Keep README configuration details aligned with `values.yaml` and templates.
- Validate chart changes with `helm lint .` and `helm template .`.

## Conventions

- Commit messages: `path: description`; do not use conventional-commit prefixes.
- Branches: `<type>/<issue-number>-<slug>` (for example, `enh/11-gateway-api`).
