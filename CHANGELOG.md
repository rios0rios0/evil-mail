# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

This file is not edited by hand. Every change writes its own fragment under
`.changes/unreleased/` with [chlog](https://github.com/luizjhonata/chlog), and a release compiles
the pending fragments into a version section here — so two branches each adding an entry no
longer touch the same lines, and a rebase that used to conflict on this file now conflicts on
nothing.

When a new release is proposed:

1. Create a new branch `bump/x.x.x` (this isn't a long-lived branch!!!);
2. The fragments pending under `.changes/unreleased/` are compiled into a version section by `chlog batch auto && chlog merge` (AutoBump does this for you — it reads the fragments directly);
3. Open a Pull Request with the bump version changes targeting the `main` branch;
4. When the Pull Request is merged, a Git tag matching the released version must be created on `main` (`git tag vx.x.x && git push origin vx.x.x`), or through the repository's [releases page](https://github.com/rios0rios0/evil-mail/releases/new).

Releases to productive environments should run from a tagged version.
Exceptions are acceptable depending on the circumstances (critical bug fixes that can be cherry-picked, etc.).

## [Unreleased]

## [2.3.1] - 2026-09-01

### Changed

- refreshed `CLAUDE.md` to describe the repository's Claude review/mention CI workflows instead of claiming no CI exists
- refreshed the `.github/copilot-instructions.md` repository structure tree to list the `.changes/`, `.github/skills/`, and `.github/workflows/` directories

## [2.3.0] - 2026-08-28

### Added

- added the Claude automated code review and `@claude` mention responder workflows, `claude-review.yaml` and `claude-mention.yaml`, matching the `reusable-claude-review.yaml` / `reusable-claude-mention.yaml` definitions they call in `rios0rios0/pipelines`, authenticating with the `CLAUDE_CODE_OAUTH_TOKEN` secret

### Fixed

- restored the `.changes/unreleased/` directory with a `.gitkeep`, so the release tooling keeps recognising this project as `chlog`-based after a release consumes the last fragment. Git tracks files rather than directories, so the bump commit that removed the final fragment removed the directory too, and the next run read the empty `[Unreleased]` section as "nothing to release"
- restored the `id-token: write` permission on both Claude workflow callers. Without it the caller grants less than the reusable workflow declares, which GitHub rejects before the job starts -- runs ended in `startup_failure`. The action needs the scope because `setupGitHubToken()` exchanges a GitHub OIDC token for the GitHub App token it posts with, unless a `github_token` is passed explicitly.

### Removed

- removed the unused `id-token: write` permission from the Claude workflow callers, and changed `claude-review.yaml`'s display name to `Claude Review` so it matches its file name and its `Claude Mention` sibling. `anthropics/claude-code-action` needs `id-token: write` only for workload identity federation or the Bedrock / Vertex / Foundry OIDC paths; these authenticate with `claude_code_oauth_token`, so the scope allowed minting OIDC tokens for any audience without ever being used.

## [2.2.0] - 2026-08-26

### Added

- added a tailored `code-review` skill under `.github/skills/` so GitHub Copilot reviews changes against the [rios0rios0/guide](https://github.com/rios0rios0/guide/wiki) standards and this repository's own load-bearing invariants

### Changed

- changed the changelog to [chlog](https://github.com/luizjhonata/chlog) fragments: a change now writes its own YAML file under `.changes/unreleased/` through `chlog new --kind <Kind> --body "..."`, and `CHANGELOG.md` is GENERATED from them at release time by `chlog batch auto && chlog merge`. That is the one thing a single shared file cannot do — two branches each adding an entry no longer touch the same lines, so a rebase that used to conflict on `CHANGELOG.md` now conflicts on nothing. The `[Unreleased]` section was empty, so nothing had to be carried across. AutoBump already reads the fragments directly, so the release flow is unchanged.

### Fixed

- fixed step 4 of the release instructions in `CHANGELOG.md`, replacing an unfilled placeholder with the concrete tag command and the releases page link

## [2.1.1] - 2026-06-18

### Changed

- refreshed `.github/copilot-instructions.md` to list `CLAUDE.md` in the repository structure tree

## [2.1.0] - 2026-06-09

### Added

- created `CLAUDE.md` to provide Claude Code with build, architecture, and convention guidance for the Delphi 7 codebase

## [2.0.1] - 2026-05-08

### Changed

- refreshed `.github/copilot-instructions.md` to include `CHANGELOG.md` in the repository structure tree

## [2.0.0] - 2019-01-04

The changes weren't tracked until this version.
