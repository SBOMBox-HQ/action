# Changelog

Notable changes to the SBOMBox GitHub Action, the pre-commit hook and the GitLab CI component. Releases follow [semantic versioning](https://semver.org), and the major tag (`v1`) always points at the newest `v1.x.y`.

## v1.0.0

First public release.

- Generate an SBOM from a directory or a container image, in CycloneDX or SPDX.
- Score it, with an optional `fail-under` quality gate.
- Check compliance profiles such as `ntia-2021`, with results in the job summary.
- Upload to SBOMBox keylessly with the job's OIDC identity, or with an API token. The upload happens before the quality gate, so failed builds still leave a record.
- Keep the SBOM and score as a workflow artifact.
- pre-commit hook `sbombox-check` and a GitLab CI component, published from this repository at the same tags.
