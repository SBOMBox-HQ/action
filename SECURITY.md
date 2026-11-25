# Security policy

This action runs inside your CI and handles your SBOMs and the job's identity token, so we treat its security as seriously as the SBOMBox platform's.

## Reporting a vulnerability

**Please don't open a public issue, discussion or pull request for a security problem.**

Report it privately, either way:

- **GitHub:** use **Report a vulnerability** on this repository's [Security tab](https://github.com/sbombox-hq/action/security/advisories/new).
- **Email:** `dpo@sbombox.com`. The same channel is listed in [sbombox.com/.well-known/security.txt](https://sbombox.com/.well-known/security.txt).

Please include:

- What the issue is and what an attacker could do with it.
- Steps to reproduce, or a proof of concept.
- The action version or commit SHA you tested.
- Whether the issue is already public.

## What to expect

- **Acknowledgement** within 3 business days.
- **A fix** within 14 days for confirmed critical and high-severity issues, depending on complexity.
- **Coordinated disclosure.** We publish a GitHub security advisory once a fix is released, and credit you unless you'd rather stay anonymous.
- **Safe harbor.** We won't pursue legal action against researchers who report in good faith and follow this policy.

## Supported versions

Security fixes go into the latest release of the current major version, and the major tag (`v1`) moves to it. If you pin a version or commit SHA, update the pin when an advisory is published.

## Scope

In scope: this action (`action.yml`), the pre-commit hook, the GitLab CI component, and how they handle tokens, credentials and SBOM files.

The SBOMBox CLI, API and web app are covered by the same reporting channel and policy. Issues in third-party tools the action installs, such as syft, should go to those projects first. We'll still help if their use in this action makes an issue worse.
