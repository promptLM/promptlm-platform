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

1. Open a tracking issue titled `release: promptlm-platform X.Y.Z` and list
   every library version going into the train.
2. Bump `<version>` in the aggregator and both modules:
   `mvn versions:set -DnewVersion=X.Y.Z -DprocessAllModules=true`.
3. Commit on `main` with message `chore: release X.Y.Z`.
4. Tag: `git tag -a vX.Y.Z -m "promptlm-platform X.Y.Z"` and push.
5. Create the GitHub release. `release.yml` runs on the `release: created`
   event and publishes both artifacts to GitHub Packages.
6. Bump `main` back to `X.Y.(Z+1)-SNAPSHOT`.

## Changelog

The canonical changelog is the
[GitHub releases page](https://github.com/promptLM/promptlm-platform/releases).
Each release's body lists what changed in the BOM and parent since the
previous one. Migration notes for major bumps live under a `## Migration`
heading in the same body.
