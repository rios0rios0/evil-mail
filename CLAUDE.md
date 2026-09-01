# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Evil Mail is a bulk email sender written in Object Pascal (Delphi 7), discontinued 2013-12-05 and preserved as an archive. Do not propose features, refactors, or library upgrades. Limit changes to documentation and historical corrections. See `README.md` for the feature list and `.github/copilot-instructions.md` for the parallel Copilot guidance — keep the two in substantive agreement.

## Build / test

- No build, test, or lint tooling runs here — there is nothing to execute locally. The only CI is two GitHub Actions callers (`.github/workflows/claude-review.yaml`, `claude-mention.yaml`) that delegate to `rios0rios0/pipelines` reusable workflows and touch no Delphi code. Don't add commands that can't be run here.
- Compilation requires Windows + Borland Delphi 7 (or compatible RAD Studio) with Indy 9 installed. Open `EM.dpr`, compile with `Ctrl+F9` or build-and-run with `F9`.
- `Clear.bat` deletes Delphi build artifacts (`*.dcu`, `*.dsk`, `*.cfg`, etc.) from the working directory.

## Architecture invariants

- `EM.dpr` creates both forms at startup: `TForm1` (`UEM.pas`, main window) and `TForm2` (`UEMP.pas`, HTML preview).
- `UEM.pas` links the embedded OpenSSL DLLs via `{$R Resources\Dlls.res}`. On first run, `FormCreate` extracts `libeay32.dll` / `ssleay32.dll` to `C:\Windows\System32\` if absent — a non-obvious, privileged side effect, not a build-time dependency.
- HTML preview couples the two forms through a file on disk, not memory: `btnvisuhtmlClick` writes the memo to `Preview.HTML` in the current dir, then `TForm2.FormShow` points the `TWebBrowser` at that path. Changing one path breaks the other.
- The send loop (`btnenviarmsgClick`) connects and disconnects `TIdSMTP` once per recipient and swallows per-send failures into the status bar rather than aborting.
- The `ContentType := 'text/plain'` then `'text/HTML'` sequence around `Body.Text` in the send loop is intentional Indy 9 behavior; don't "simplify" it.

## Conventions

- UI strings are Brazilian Portuguese. Do not translate them.
- `.pas` sources are ANSI/Latin-1 encoded; the mojibake in string literals (e.g. `Informa��o`) is the original encoding, not a bug to fix — do not re-encode to UTF-8.
- Delphi component naming uses lowercase type prefixes: `edt`, `btn`, `lbl`, `mmo`, `lst`, `grp`, `pb`, `stat`, `dlg`. Event handlers follow Delphi's `<Component><Event>` auto-naming.
- Windows-only Win32: file paths, DLL handling, and `WinInet`/`TWebBrowser` usage are all Windows-specific. `Resources/Dlls.res` is a compiled binary resource and cannot be edited as text.

<!-- chlog:start -->
## Changelog (chlog) — MANDATORY

If the repository you are working in uses chlog (a `.chlog.yaml` or `.chlog.yml`
config file, or a `.changes/` directory, exists at the project root), the
following is binding and ALWAYS applies: whenever you make ANY change, you MUST
create a changelog fragment as part of the same change — automatically, without
being asked, before committing.

- Do NOT edit CHANGELOG.md directly; it is generated from fragments.
- Create the fragment with:
  `chlog new --kind <Kind> --body "<imperative description>"`
- Valid kinds: Added, Changed, Deprecated, Removed, Fixed, Security
- Choose the kind that best matches the change (e.g., new feature → Added,
  bug fix → Fixed, behavior change → Changed, removal → Removed, security fix → Security).
- If the change is backward-INCOMPATIBLE with the public API (a breaking
  change), you MUST add the `--breaking` flag:
  `chlog new --kind <Kind> --breaking --body "<description>"`.
  This is the ONLY thing that triggers a major version bump — the kind alone
  never does (per SemVer, major = incompatible change). When unsure whether a
  change breaks compatibility, ask the user instead of guessing.
- Fragments are YAML files in `.changes/unreleased/`; stage them with your commit.
- `chlog check` fails the build when a fragment is missing — never skip it.
<!-- chlog:end -->
