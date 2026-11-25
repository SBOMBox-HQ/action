# Contributing

Thanks for helping make the SBOMBox action better.

## How this repository works

This repository is a **published mirror**. The action is developed in the SBOMBox monorepo, where it's tested alongside the CLI and API it depends on, and each release is copied here automatically. Anything committed directly to this repository is replaced by the next release.

So we can't merge pull requests here as they are. A pull request is still a great way to show us a fix: we'll review it, apply the change in the monorepo, credit you in the [changelog](CHANGELOG.md) and close the pull request with a link to the release that includes it.

## Reporting a bug

Send it through [sbombox.com/contact](https://sbombox.com/contact), or open an issue here. Please include:

- The action version or commit SHA (`sbombox-hq/action@...`).
- The runner (for example `ubuntu-latest` or a self-hosted runner and its OS).
- The relevant part of your workflow file, with project IDs and tokens removed.
- The job log from the **SBOMBox** steps. Logs never contain your token, but check before posting.

**Security issues are different.** Don't report them in public. Follow [SECURITY.md](SECURITY.md) instead.

## Suggesting a feature

Tell us what you're trying to do and what's getting in the way, rather than only the change you have in mind. Most requests turn out to need a new input, a better default or a docs fix, and knowing the goal helps us pick the right one.

## Proposing a change

If you'd like to send a fix:

1. Keep it small and focused on one problem.
2. Keep the action keyless by default. Changes must not require new secrets or a write-scoped `GITHUB_TOKEN`.
3. Pin any third-party action you add to a full commit SHA, with the version in a comment.
4. Don't add network calls to hosts other than sbombox.com, api.sbombox.com and the generator downloads the action already makes, unless the change is about that.
5. Explain how you tested it, ideally with a link to a workflow run.

By sending a contribution, you agree it's licensed under this repository's [Apache License 2.0](LICENSE).

## Code of conduct

Everyone taking part is expected to follow our [Code of Conduct](CODE_OF_CONDUCT.md).
