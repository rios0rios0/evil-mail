# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Evil Mail is a bulk email sender written in Object Pascal (Delphi 7), discontinued 2013-12-05 and preserved as an archive. Do not propose features, refactors, or library upgrades. Limit changes to documentation and historical corrections. See `README.md` for the feature list and `.github/copilot-instructions.md` for the parallel Copilot guidance — keep the two in substantive agreement.

## Build / test

- No automated build, tests, lint, or CI exist in the repo. Don't add commands that can't be run here.
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
