# promptlm-platform

Shared build governance for all promptLM repositories. Implements the
hybrid BOM + parent layout described in
[ADR 0004](https://github.com/promptLM/promptlm-test-support/blob/main/docs/adr/0004-platform-parent-and-bom-proposed.md)
in `promptlm-test-support`.

## Artifacts

| Artifact                       | Purpose                                                                                                                              |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `dev.promptlm:promptlm-bom`    | Pure BOM — pinned dependency versions (Spring Boot anchor). Imported via `<scope>import</scope>` from any consumer.                  |
| `dev.promptlm:promptlm-parent` | Thin build-policy parent — extends the BOM, sets `<maven.compiler.release>17</maven.compiler.release>`, pins plugin versions, `<proc>none</proc>`. |

Both ship at the same version (lockstep — ADR 0004 D-Open-Q-7).

## Consuming

### Internal libraries (own the platform parent fully)

```xml
<parent>
    <groupId>dev.promptlm</groupId>
    <artifactId>promptlm-parent</artifactId>
    <version>1.0.0</version>
</parent>
```

Inherits dependency management (transitively from `promptlm-bom`) **and**
build policy (Java 17, plugin versions, `<proc>none</proc>`).

### External consumers (have their own parent — Spring Boot, corporate, etc.)

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>dev.promptlm</groupId>
            <artifactId>promptlm-bom</artifactId>
            <version>1.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Inherits pinned versions only — no build policy.

### Repository setup

Artifacts are published to GitHub Packages. Add this to `~/.m2/settings.xml`:

```xml
<servers>
    <server>
        <id>github</id>
        <username>YOUR_GITHUB_USERNAME</username>
        <password>YOUR_GITHUB_PAT_WITH_READ_PACKAGES</password>
    </server>
</servers>
```

And in any consumer pom, declare the repository:

```xml
<repositories>
    <repository>
        <id>github</id>
        <url>https://maven.pkg.github.com/promptLM/promptlm-platform</url>
    </repository>
</repositories>
```

## Versioning

Semver on the BOM — see [`RELEASING.md`](./RELEASING.md). The 1.x train
anchors **Spring Boot 3.5.x**. The Spring Boot 3 → 4 migration is planned as
the 2.0.0 train.

## Layout

```
promptlm-platform/
├── pom.xml                     # aggregator
├── promptlm-bom/
│   └── pom.xml                 # pure BOM (dependencyManagement only)
└── promptlm-parent/
    └── pom.xml                 # build-policy parent, <parent> = promptlm-bom
```
