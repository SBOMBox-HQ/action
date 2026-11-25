# SBOMBox GitHub Action

Generate, score, check and upload an SBOM in one step. Keyless: no secrets needed.

The action builds a Software Bill of Materials (SBOM) inside your own runner, scores it, checks it against your compliance profiles and uploads it to [SBOMBox](https://sbombox.com) with the verified repository, commit and run attached. Your source code never leaves the runner.

## Quick start

```yaml
name: SBOM
on:
  push:
    branches: [main]
  pull_request:

permissions:
  id-token: write   # keyless sign-in to SBOMBox
  contents: read

jobs:
  sbom:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: sbombox-hq/action@v1
        with:
          project: <project-uuid>
```

Don't have a project yet? Run `sbombox init` in your repository (install with `curl -fsSL https://sbombox.com/install.sh | sh`). It creates the project, trusts this repository's CI and writes this workflow for you.

Every run is recorded. The SBOM is uploaded *before* any quality gate fails the job, and a copy is kept as a workflow artifact, so a failed build still leaves evidence.

## Examples

**Fail the build on a low score and check compliance**

```yaml
      - uses: sbombox-hq/action@v1
        with:
          project: <project-uuid>
          fail-under: 7
          profiles: ntia-2021
```

**Scan a container image instead of the source tree**

```yaml
      - uses: sbombox-hq/action@v1
        with:
          project: <project-uuid>
          image: ghcr.io/acme/api:1.4.0
          platform: linux/arm64
```

The action uses the SBOM that BuildKit attached to the image (`docker buildx build --sbom=true`) when there is one, and scans the image with syft otherwise. For private registries, run `docker/login-action` first.

**Several services in one repository**

```yaml
    strategy:
      matrix:
        service: [api, web, worker]
    steps:
      - uses: actions/checkout@v7
      - uses: sbombox-hq/action@v1
        with:
          project: acme/${{ matrix.service }}
          path: services/${{ matrix.service }}
          artifact-name: sbom-${{ matrix.service }}
```

**Generate and score without an SBOMBox account**

```yaml
      - uses: sbombox-hq/action@v1
        with:
          project: local
          upload: false
          fail-under: 7
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `project` | *(required)* | SBOMBox project UUID, or `org/project` slug. Not a secret. |
| `path` | `.` | Directory to scan. Ignored when `image` is set. |
| `image` | | Container image to scan instead of a directory. |
| `platform` | `linux/amd64` | Platform of a multi-platform `image`, e.g. `linux/arm64`. |
| `format` | `cyclonedx` | SBOM format: `cyclonedx` or `spdx`. |
| `fail-under` | `0` | Fail the job if the quality score (0-10) is below this. `0` turns the gate off. |
| `profiles` | | Compliance profiles to check, comma-separated, e.g. `ntia-2021`. Results appear in the job summary. |
| `upload` | `auto` | `auto` uploads when the job can authenticate and skips with a notice otherwise (for example, pull requests from forks). `true` always uploads and fails without credentials. `false` never uploads. |
| `token` | | SBOMBox API token. Leave empty to sign in keylessly with the job's OIDC identity. |
| `output` | `sbom.json` | Where to write the SBOM. |
| `artifact-name` | `sbom` | Workflow artifact holding the SBOM and score. Set one per matrix job. Empty skips the artifact. |
| `version` | `latest` | Pin the SBOMBox CLI version, e.g. `0.2.0`. |
| `generators` | *auto* | SBOM generators to install: `syft`, `cyclonedx-py`, `cyclonedx-npm`, `cdxgen`, `auto`, `all` or `none`. Add `cdxgen` for Java and Gradle projects. |
| `generator` | | Force which generator runs, in order of preference, e.g. `npm-sbom` or `cyclonedx-npm,syft`. |
| `syft-version` | `v1.57.1` | syft version to install. |
| `installer-url` | `https://sbombox.com/install.sh` | Installer to run. Point at an internal mirror if runners can't reach sbombox.com. |
| `api-url` | `https://api.sbombox.com` | SBOMBox API URL, for self-hosted deployments. |

The full descriptions are in [action.yml](action.yml).

## Outputs

| Output | Description |
| --- | --- |
| `sbom-path` | Path of the generated SBOM. |
| `score` | Quality score, 0-10. |
| `grade` | Letter grade for the score. |
| `uploaded` | `true` when the SBOM was uploaded. |
| `sbom-id` | ID of the uploaded SBOM in SBOMBox. |
| `provenance-level` | `P2` when uploaded keylessly (identity verified by SBOMBox), `P1` with a token, `P0` otherwise. |
| `policy-status` | `pass` or `fail`: the result of the `fail-under` gate. |

## Other CI systems

This repository also publishes, at the same version tags:

- **pre-commit:** blocks a commit when the SBOM score drops below your threshold. Nothing is uploaded.

  ```yaml
  repos:
    - repo: https://github.com/sbombox-hq/action
      rev: v1
      hooks:
        - id: sbombox-check
          args: ["--fail-under", "7"]
  ```

- **GitLab CI:** the same generate, score and upload flow, keyless with GitLab's OIDC token.

  ```yaml
  include:
    - remote: 'https://raw.githubusercontent.com/sbombox-hq/action/v1/gitlab/sbombox-component.yml'
  variables:
    SBOMBOX_PROJECT: "<project-uuid>"
  ```

See the [CI docs](https://sbombox.com/docs/github-action) for the full setup.

## Versioning and pinning

Releases follow [semantic versioning](https://semver.org). Each release gets an immutable tag (`v1.2.0`), and the major tag (`v1`) moves to the newest release in that line. Breaking changes only ship in a new major version.

- `@v1` gets fixes and new features automatically.
- `@v1.2.0` stays on one release.
- `@<full-commit-sha>` is the strictest option, and what most supply-chain policies require. Dependabot and Renovate can keep a SHA pin up to date.

Release notes are in [CHANGELOG.md](CHANGELOG.md).

## Security

The action never sends your source code anywhere. It needs `id-token: write` only to prove the job's identity to SBOMBox. It never asks for a write-scoped `GITHUB_TOKEN`.

To report a vulnerability, see [SECURITY.md](SECURITY.md). Please don't open a public issue.

## Support

- Docs: [sbombox.com/docs](https://sbombox.com/docs)
- Questions and bug reports: [sbombox.com/contact](https://sbombox.com/contact)
- Contributing: [CONTRIBUTING.md](CONTRIBUTING.md)

## License

[Apache License 2.0](LICENSE).
