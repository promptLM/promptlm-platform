# promptlm-platform

Shared build governance for all promptLM repositories.

## Artifacts

| Artifact | Purpose | Status |
|---|---|---|
| `dev.promptlm:promptlm-parent` | Inheritable parent POM — Java version, plugin versions, shared test deps, plugin management, profiles. | Extracted from `promptlm-app` (this proposal). |
| `dev.promptlm:promptlm-platform` (BOM) | Importable BOM — pinned versions of every dependency the platform vouches for. | Planned for follow-up; not yet present. |

## Versioning

- `promptlm-parent` starts at `1.0.0-SNAPSHOT`. It evolves slowly — bump only when build policy changes (Java version, plugin versions, profile semantics).
- The BOM, once added, will follow the team's chosen cadence (see `docs/proposals/shared-build-governance.md` in `promptlm-app`).

## Publishing

GitHub Packages, target: `https://maven.pkg.github.com/promptlm/promptlm-platform`.

## Consuming

In a sibling repo's root pom:

```xml
<parent>
    <groupId>dev.promptlm</groupId>
    <artifactId>promptlm-parent</artifactId>
    <version>1.0.0</version>
</parent>
```

During the dry-run, sibling repos resolve via `<relativePath>../promptlm-platform/pom.xml</relativePath>`.
