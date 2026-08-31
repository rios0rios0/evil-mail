# Copilot Instructions for Evil Mail

## Project Overview

Evil Mail is a historical bulk email sending utility built with **Object Pascal (Delphi 7)** and discontinued in **December 2013**. The repository is preserved as an educational and archival reference. No new features or bug fixes are planned.

## Repository Structure

```
evil-mail/
├── .changes/
│   └── unreleased/               # chlog fragments awaiting the next release
├── .github/
│   ├── copilot-instructions.md   # This file
│   ├── skills/
│   │   └── code-review/
│   │       └── SKILL.md          # Copilot code-review skill tailored to this repo
│   └── workflows/
│       ├── claude-review.yaml    # Claude PR review (calls rios0rios0/pipelines)
│       └── claude-mention.yaml   # @claude responder (calls rios0rios0/pipelines)
├── EM.dpr                        # Delphi project file – application entry point, creates Form1 and Form2
├── EM.res                        # Compiled application resources (icon)
├── UEM.pas                       # Main form unit – SMTP config, email composition, sending logic, recipient list
├── UEM.dfm                       # Main form layout – GroupBoxes, Edit fields, Buttons, ListBox, StatusBar, ProgressBar
├── UEMP.pas                      # HTML preview form unit – loads Preview.HTML into an embedded WebBrowser
├── UEMP.dfm                      # Preview form layout – fullscreen WebBrowser component
├── Clear.bat                     # Cleanup script – removes Delphi temporary files (*.dcu, *.dsk, *.cfg, etc.)
├── Imgs/
│   └── Icon.ico                  # Application icon
├── Resources/
│   ├── Dlls.res                  # Compiled resource file containing embedded OpenSSL DLLs
│   ├── libeay32.dll              # OpenSSL cryptographic library (source for embedding)
│   └── ssleay32.dll              # OpenSSL SSL/TLS library (source for embedding)
├── CHANGELOG.md
├── CLAUDE.md                     # Guidance for Claude Code sessions (companion to this file)
├── CONTRIBUTING.md
├── LICENSE                       # GNU General Public License v3.0
└── README.md
```

## Technology Stack

| Component        | Details                                                                               |
|------------------|---------------------------------------------------------------------------------------|
| **Language**     | Object Pascal (Delphi 7)                                                              |
| **IDE**          | Borland Delphi 7 (or compatible: Embarcadero RAD Studio)                              |
| **SMTP Library** | Indy 9 (`TIdSMTP`, `TIdMessage`, `TIdAttachment`, `TIdSSLIOHandlerSocket`)            |
| **SSL**          | OpenSSL (`libeay32.dll`, `ssleay32.dll`) embedded as Win32 resources in `Dlls.res`   |
| **HTML Preview** | `TWebBrowser` (OLE/COM Internet Explorer control via `SHDocVw`)                       |
| **Connectivity** | WinInet API (`InternetGetConnectedState`)                                              |
| **UI Styles**    | `TXPManifest` for Windows XP visual themes                                            |
| **Platform**     | Windows only (Win32)                                                                  |

## Architecture

The application has two forms:

1. **Form1 (`TForm1` in `UEM.pas`)** – the main window with three `TGroupBox` sections:
   - **Settings** (`grpdefi`): SMTP host (`edtsmtp`), port (`edtporta`), username (`edtusuario`), password (`edtsenha`) with show/hide toggle (`chksenha`)
   - **Message** (`grpmsg`): sender email (`edtremetente`), sender display name (`edtnomeremet`), subject (`edtassunto`, defaults to `(Sem Assunto)`), HTML body memo (`mmocorpomsg`), and optional attachment (`edtanexo`)
   - **Recipient List** (`grplista`): a `TListBox` (`lstdestinos`) with a right-click popup menu for add/modify/remove/clear/import/export operations

2. **Form2 (`TForm2` in `UEMP.pas`)** – the HTML preview window that saves memo contents to `Preview.HTML` in the working directory and navigates an embedded `TWebBrowser` to display it

### Email Sending Flow (`btnenviarmsgClick`)

1. `CheckConexao()` verifies internet connectivity via `WinInet`
2. Progress bar (`pb1`) and status bar (`stat1`) are initialised
3. For each recipient in `lstdestinos`, `TIdMessage` is configured with the HTML body, sender info, and subject
4. `TIdSMTP` authenticates with `atLogin` and connects to the configured host/port
5. The message is sent; the connection is disconnected after each recipient
6. On completion, a success dialog is shown

### First-Run Behaviour (`FormCreate`)

On startup, if `libeay32.dll` or `ssleay32.dll` are not present in `C:\Windows\System32\`, they are extracted from the embedded `Dlls.res` resource and written there to enable SSL/TLS.

## Build Instructions (Historical)

There is no automated build system. Compilation requires a Windows machine with Borland Delphi 7 or a compatible IDE.

1. Open `EM.dpr` in **Borland Delphi 7** (or Embarcadero RAD Studio)
2. Ensure **Indy 9** components are installed in the IDE
3. Compile: **Ctrl+F9** (compile only) or **F9** (build and run)

> **Note:** Run `Clear.bat` to remove Delphi temporary files (`.dcu`, `.dsk`, `.cfg`, etc.) from the working directory.

## Tests and Linting

This project has no automated tests or linters, and no build or deployment pipeline. The only workflows are `.github/workflows/claude-review.yaml` and `.github/workflows/claude-mention.yaml`, which call the shared Claude reusable workflows in `rios0rios0/pipelines` and need the `CLAUDE_CODE_OAUTH_TOKEN` secret. The project is a preserved historical archive and no testing infrastructure exists.

## Development Workflow

Because this project is archived and no longer maintained, there is no active development workflow. If making historical corrections or documentation updates:

1. Edit the relevant `.pas`, `.dfm`, or documentation files
2. Open the project in Delphi 7 / RAD Studio to verify compilation if source changes are made
3. Submit a pull request with a clear description of the archival correction

## Coding Conventions

- **Language:** Object Pascal with Delphi 7 naming conventions
  - Form component names use lowercase prefixes (e.g., `edt` for `TEdit`, `btn` for `TButton`, `lbl` for `TLabel`, `mmo` for `TMemo`, `lst` for `TListBox`, `grp` for `TGroupBox`, `pb` for `TProgressBar`, `stat` for `TStatusBar`, `dlg` for dialogs)
  - Event handlers follow Delphi's auto-generated pattern: `<ComponentName><EventName>` (e.g., `btnenviarmsgClick`)
- **UI strings:** The application UI is in Brazilian Portuguese (Português do Brasil)
- **Error handling:** Uses `try/except` blocks with `MessageBox` dialogs for user-facing errors; no structured logging

## Common Tasks

| Task                            | How                                                                          |
|---------------------------------|------------------------------------------------------------------------------|
| Build the project               | Open `EM.dpr` in Delphi 7, press **Ctrl+F9**                                |
| Run the application             | Press **F9** in Delphi 7, or run the compiled `EM.exe` directly              |
| Clean build artefacts           | Run `Clear.bat` in the project root                                          |
| Add a recipient                 | Right-click the recipient list → **Adicionar E-Mail**                        |
| Import a recipient list         | Right-click the list → **Abrir Lista** (plain text file, one email per line) |
| Preview HTML message body       | Click **Visualizar HTML** button in the main form                            |
| Attach a file                   | Click the attachment button; note that HTML formatting is disabled with attachments |

## Notes for AI Assistants

- This is a **Windows-only Win32 application**; all file paths, DLL handling, and UI controls are Windows-specific
- The project is **discontinued** – avoid suggesting feature additions or library upgrades
- The source code contains **Brazilian Portuguese** UI strings; do not translate them
- The `Resources/Dlls.res` file is a binary compiled resource – it cannot be edited as plain text
- OpenSSL DLLs (`libeay32.dll`, `ssleay32.dll`) in the `Resources/` folder are **legacy versions** bundled for historical compatibility only

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
