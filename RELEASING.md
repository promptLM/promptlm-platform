# Releasing promptlm-platform

This document describes how to cut a release of the
`dev.promptlm:promptlm-dependencies` + `dev.promptlm:promptlm-parent` pair.

## Versioning

The BOM carries the compatibility contract and `promptlm-parent` ships in
lockstep with the BOM (see [ADR 0004](https://github.com/promptLM/promptlm-test-support/blob/main/docs/adr/0004-platform-parent-and-bom-proposed.md)
D2, D-Open-Q-7). Bumps follow semver:

- **Major (`X.0.0`)** — at least one contained library or third-party anchor
  has a breaking change.
- **Minor (`X.Y.0`)** — additive: new library entry, new build-policy plugin,
  dependency minor bump.
- **Patch (`X.Y.Z`)** — bugfix only; no API change.

Libraries keep their own semver inside the BOM; the BOM stamps a tested
combination.

## Cutting a release

Releases are driven by [release-please](https://github.com/googleapis/release-please)
from Conventional Commits — manual `mvn versions:set` + tag pushing is no longer
the path. The publish itself is delegated to the org-wide reusable workflow
[`release-java-central.yml`](https://github.com/promptLM/.github/blob/main/.github/workflows/release-java-central.yml).

1. Open a tracking issue titled `release: promptlm-platform X.Y.Z` and list
   every library version going into the train.
2. Land Conventional Commits on `main` (`feat:`, `fix:`, `chore:` etc.).
   release-please opens / updates a Release PR with the computed version
   bump in `pom.xml`, `promptlm-dependencies/pom.xml`, `promptlm-parent/pom.xml`,
   and the changelog.
3. Review the Release PR. Once green, merge it. release-please then creates
   the GitHub Release + tag `vX.Y.Z` via the GitHub API.
4. `release-please.yml` chains directly to the org `release-java-central.yml`
   reusable workflow (no PAT — releases created with the default
   `GITHUB_TOKEN` don't re-trigger workflows, so we call it inline). The
   workflow runs validate → verify → smoke-sign → deploy and uploads to the
   Central Portal in `VALIDATED` state.
5. The `promote` job pauses on the `maven-central-publish` environment for
   human approval. Approving promotes the deployment to `PUBLISHED`
   (irreversible). Rejecting leaves the deployment in the Portal UI for up
   to 90 days; it can be dropped without publishing.

For a manual or dry-run release, dispatch `release-dispatch.yml` with
`version=X.Y.Z` and optionally `dry-run=true` (builds + signs without
uploading anything).

### First-release bootstrap

The release-please manifest starts at `0.0.0`. To cut `0.1.0` as the first
release, include a `Release-As: 0.1.0` footer on the commit that triggers
release-please (or apply the `release-as: 0.1.0` label to the Release PR).

## Changelog

The canonical changelog is the
[GitHub releases page](https://github.com/promptLM/promptlm-platform/releases).
Each release's body lists what changed in the BOM and parent since the
previous one. Migration notes for major bumps live under a `## Migration`
heading in the same body.
