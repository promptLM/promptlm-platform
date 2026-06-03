# promptlm-platform

Shared build governance for all promptLM repositories. Implements the
hybrid BOM + parent layout described in
[ADR 0004](https://github.com/promptLM/promptlm-test-support/blob/main/docs/adr/0004-platform-parent-and-bom-proposed.md)
in `promptlm-test-support`.

## Artifacts

| Artifact                              | Purpose                                                                                                                              |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `dev.promptlm:promptlm-dependencies`  | Pure BOM — pinned dependency versions (Spring Boot anchor). Imported via `<scope>import</scope>` from any consumer.                  |
| `dev.promptlm:promptlm-parent`        | Thin build-policy parent — extends the BOM, sets `<maven.compiler.release>21</maven.compiler.release>`, pins plugin versions, `<proc>none</proc>`. |

Both ship at the same version (lockstep — ADR 0004 D-Open-Q-7).

## Consuming

### Internal libraries (own the platform parent fully)

```xml
<parent>
    <groupId>dev.promptlm</groupId>
    <artifactId>promptlm-parent</artifactId>
    <version>0.1.0</version>
</parent>
```

Inherits dependency management (transitively from `promptlm-dependencies`)
**and** build policy (Java 21, plugin versions, `<proc>none</proc>`).

### External consumers (have their own parent — Spring Boot, corporate, etc.)

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>dev.promptlm</groupId>
            <artifactId>promptlm-dependencies</artifactId>
            <version>0.1.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Inherits pinned versions only — no build policy.

### Repository setup

Artifacts are published to [Maven Central](https://central.sonatype.com/namespace/dev.promptlm).
No `<repositories>` block and no `~/.m2/settings.xml` configuration is required —
Central is on every Maven installation's default resolver list.

## Versioning

Semver on the BOM — see [`RELEASING.md`](./RELEASING.md) for the bump rules
and the release process.

## Layout

```
promptlm-platform/
├── pom.xml                     # aggregator
├── promptlm-dependencies/
│   └── pom.xml                 # pure BOM (dependencyManagement only)
└── promptlm-parent/
    └── pom.xml                 # build-policy parent, <parent> = promptlm-dependencies
```
