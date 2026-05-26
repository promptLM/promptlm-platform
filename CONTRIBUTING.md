# Contributing to promptLM

Thanks for your interest in contributing! This repository hosts
`dev.promptlm:promptlm-dependencies` (the BOM) and
`dev.promptlm:promptlm-parent` (the build-policy parent POM) for the promptLM
project.

This document covers the basics of opening a pull request, our commit
conventions, and how to build the project locally. Please also read the
[Code of Conduct](CODE_OF_CONDUCT.md) and the [Security Policy](SECURITY.md).

## Reporting Issues

- **Bugs and feature requests:** open a GitHub issue with a clear repro or
  motivation.
- **Security vulnerabilities:** do **not** open a public issue — follow
  [SECURITY.md](SECURITY.md) instead.

## Pull Request Flow

1. **Fork** the repository and create a feature branch off `main`:
   ```sh
   git checkout -b feat/short-topic-name
   ```
2. **Make your changes.** Keep PRs small and focused — one logical change per
   PR. Update or add tests where applicable.
3. **Run the build** locally (see below) and make sure it passes.
4. **Push** the branch to your fork and open a pull request against
   `promptLM/promptlm-platform`'s `main` branch.
5. A maintainer will review. Address feedback by pushing additional commits
   (don't force-push during review unless asked).
6. Once approved, a maintainer will merge. We default to **squash-merge** so
   the final commit message follows Conventional Commits (see below).

## Conventional Commits

Commit messages and PR titles follow the
[Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<optional scope>): <short summary>

<optional body>

<optional footer(s)>
```

Common types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `build`,
`ci`, `perf`, `revert`. Breaking changes are flagged with `!` after the type
(e.g. `feat!: rename parent artifactId`) or a `BREAKING CHANGE:` footer.

Examples:

- `feat(bom): pin Spring AI to 1.0.0-M5`
- `fix(parent): correct surefire argLine concatenation`
- `chore(ci): bump setup-java to v4`

## Developer Certificate of Origin (DCO)

All commits must be signed off under the
[Developer Certificate of Origin](https://developercertificate.org/). This
certifies that you wrote the contribution (or otherwise have the right to
submit it under the project's open-source license).

Add a `Signed-off-by` trailer by passing `-s` to `git commit`:

```sh
git commit -s -m "feat(parent): add spotless plugin"
```

This produces a footer like:

```
Signed-off-by: Your Name <you@example.com>
```

Set your `user.name` and `user.email` in git config first; the name and email
must be real.

## Building the Project

The repo uses the Maven Wrapper, so you don't need a system-wide Maven
install. JDK 21 is required.

```sh
./mvnw clean install
```

Useful variants:

```sh
# Skip tests
./mvnw -B clean install -DskipTests

# Build just the BOM
./mvnw -pl promptlm-dependencies -am clean install

# Verify
./mvnw -B verify

# Opt-in formatting check (Spotless: removeUnusedImports + importOrder)
./mvnw -B -Pspotless verify
```

CI runs the equivalent of `mvn -B -ntp clean install` on every push and PR.

## Code Style

This module has no Java sources of its own, but `promptlm-parent` declares
`spotless-maven-plugin` in `<pluginManagement>` so downstream consumers can
opt into it. If you contribute Java code to a **consumer** repository, run:

```sh
./mvnw spotless:apply
```

License headers are enforced by the pre-commit hook
(`.pre-commit-config.yaml`) and the `apache/skywalking-eyes/header` GitHub
Action — no Maven plugin involved.

before committing.

## Releasing

Maintainers cut releases following [RELEASING.md](RELEASING.md). Releases are
published to GitHub Packages by the
[`release.yml`](.github/workflows/release.yml) workflow.
