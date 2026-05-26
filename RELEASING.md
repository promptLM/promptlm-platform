# Releasing promptlm-platform

This document describes the release-train cadence for the
`dev.promptlm:promptlm-dependencies` + `dev.promptlm:promptlm-parent` pair.

## Versioning policy: semver on the BOM

Per [ADR 0004](https://github.com/promptLM/promptlm-test-support/blob/main/docs/adr/0004-platform-parent-and-bom-proposed.md)
D2, the BOM carries the compatibility contract. `promptlm-parent` ships in
lockstep with the BOM (ADR 0004 D-Open-Q-7).

- **Major bump (`X.0.0`)** — at least one contained library or third-party
  anchor has a breaking change (API removal, signature change, or major
  upstream bump like Spring Boot 3 → 4).
- **Minor bump (`X.Y.0`)** — additive only: new library entry, new
  build-policy plugin, dependency minor bump.
- **Patch bump (`X.Y.Z`)** — bugfix only across contained libraries; no API
  change.

Libraries keep their own semver inside the BOM; the BOM stamps a tested
combination.

## Cadence

- **Minor releases: monthly.** Cut on the first Monday of each month if
  there is anything to ship. Skip the month if the BOM is unchanged.
- **Major releases: quarterly.** Cut at most once per calendar quarter,
  bundled with a written migration guide and the contained libraries'
  major bumps. While on the 0.x line, minor bumps may carry breaking
  changes; the first major (`1.0.0`) marks the stable contract.
- **Patch releases: as needed.** No fixed cadence; cut on demand when a
  contained library ships a bugfix that downstream repos need
  immediately.

## Train shape

| Version | Anchor                | Status                                                                 |
| ------- | --------------------- | ---------------------------------------------------------------------- |
| 0.x     | Spring Boot 4.x       | **Current.** Pre-stable line; first release `0.1.0`. Breaking changes allowed between minors. |
| 1.x     | Spring Boot 4.x       | Planned. First stable train once the BOM contents are settled.         |

## Version selection process

1. Open a tracking issue titled `release: promptlm-platform X.Y.Z` and
   list every library version going into the train.
2. Bump `<version>` in the aggregator + both modules:
   `mvn versions:set -DnewVersion=X.Y.Z -DprocessAllModules=true`.
3. Commit the version bump on `main` with message
   `chore: release X.Y.Z`.
4. Tag: `git tag -a vX.Y.Z -m "promptlm-platform X.Y.Z"` and push.
5. Create the GitHub release. The `release.yml` workflow runs on the
   `release: created` event and publishes both artifacts to GitHub
   Packages.
6. Bump `main` back to `X.Y.(Z+1)-SNAPSHOT` so subsequent commits don't
   collide with the released version.

## Changelog

The canonical changelog is the **GitHub releases page**
([promptLM/promptlm-platform/releases](https://github.com/promptLM/promptlm-platform/releases)).
Each release's body lists what changed in the BOM and parent since the
previous train. Migration notes for major bumps live in the same body
under a `## Migration` heading.
