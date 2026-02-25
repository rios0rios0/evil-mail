<h1 align="center">Evil Mail</h1>
<p align="center">
    <a href="https://github.com/rios0rios0/evil-mail/releases/latest">
        <img src="https://img.shields.io/github/release/rios0rios0/evil-mail.svg?style=for-the-badge&logo=github" alt="Latest Release"/></a>
    <a href="https://github.com/rios0rios0/evil-mail/blob/main/LICENSE">
        <img src="https://img.shields.io/github/license/rios0rios0/evil-mail.svg?style=for-the-badge&logo=github" alt="License"/></a>
</p>

A bulk email sending utility built with Object Pascal in Borland Delphi 7, featuring SMTP authentication with SSL/TLS support, HTML message composition with live preview, file attachments, and recipient list management. This project was developed for educational purposes and is no longer actively maintained (discontinued 2013-12-05).

## Features

- **SMTP Authentication with SSL/TLS** -- connects to any SMTP server with configurable host, port, username, and password using the Indy (`TIdSMTP`) component with OpenSSL support via `TIdSSLIOHandlerSocket`
- **Bulk Email Delivery** -- iterates through a recipient list and sends individual messages to each address, with a progress bar and status panel tracking delivery progress (e.g., "3 | 10 Enviando...")
- **HTML Message Body** -- composes email bodies in `text/HTML` content type with a live preview window that renders the HTML using an embedded `TWebBrowser` (Internet Explorer) control
- **File Attachments** -- supports attaching `.zip` and `.rar` files via `TIdAttachment`, with a warning that HTML formatting is not supported alongside attachments
- **Recipient List Management** -- add, modify, remove, and clear email addresses from a `TListBox`; import recipient lists from text files and export them back to disk
- **Sender Identity Spoofing** -- configurable "From" address and display name fields, allowing the sender identity to differ from the authenticated SMTP account
- **Internet Connectivity Check** -- verifies active internet connection (modem, LAN, or proxy) via `InternetGetConnectedState` from `WinInet` before attempting to send
- **Embedded OpenSSL DLLs** -- on first launch, extracts `libeay32.dll` and `ssleay32.dll` from embedded resources (`Dlls.res`) into `C:\Windows\System32\` to enable SSL/TLS connections without requiring a separate OpenSSL installation
- **XP Visual Styles** -- applies Windows XP themed controls via `TXPManifest`
- **Anti-Freeze UI** -- uses `TIdAntiFreeze` to keep the UI responsive during network operations

## Technologies

| Component | Details |
|-----------|---------|
| **Language** | Object Pascal (Delphi 7) |
| **IDE** | Borland Delphi 7 |
| **SMTP Library** | Indy 9 (`TIdSMTP`, `TIdMessage`, `TIdAttachment`, `TIdSSLIOHandlerSocket`) |
| **SSL** | OpenSSL (`libeay32.dll`, `ssleay32.dll`) embedded as Win32 resources |
| **HTML Preview** | `TWebBrowser` (OLE/COM Internet Explorer control via `SHDocVw`) |
| **Connectivity** | WinInet API (`InternetGetConnectedState`) |

## Project Structure

```
evil-mail/
├── EM.dpr                 # Delphi project file - application entry point, creates Form1 and Form2
├── EM.res                 # Compiled application resources (icon)
├── UEM.pas                # Main form unit - SMTP configuration, email composition, sending logic, recipient list
├── UEM.dfm                # Main form layout - GroupBoxes, Edit fields, Buttons, ListBox, StatusBar, ProgressBar
├── UEMP.pas               # HTML preview form unit - loads Preview.HTML into embedded WebBrowser
├── UEMP.dfm               # Preview form layout - fullscreen WebBrowser component
├── Clear.bat              # Cleanup script - removes Delphi temporary files (*.dcu, *.dsk, *.cfg, etc.)
├── Imgs/
│   └── Icon.ico           # Application icon
├── Resources/
│   ├── Dlls.res           # Compiled resource file containing embedded OpenSSL DLLs
│   ├── libeay32.dll       # OpenSSL cryptographic library (source for embedding)
│   └── ssleay32.dll       # OpenSSL SSL/TLS library (source for embedding)
├── LICENSE                # GNU General Public License v3.0
└── README.md
```

## Architecture

The application consists of two forms:

1. **Form1 (`TForm1` in `UEM.pas`)** -- the main window with three sections:
   - **Settings Group** ("Definicoes"): SMTP server host, port, username, password (with show/hide toggle)
   - **Message Group** ("Mensagem"): sender email, sender display name, subject line (defaults to "Sem Assunto"), and a memo field for the HTML message body
   - **Recipient List Group** ("Lista"): a `TListBox` with right-click context menu for add/modify/remove/clear/import/export operations

2. **Form2 (`TForm2` in `UEMP.pas`)** -- the HTML preview window that saves the memo contents to a temporary `Preview.HTML` file and navigates the embedded `TWebBrowser` to display it

### Email Sending Flow

1. `CheckConexao()` verifies internet connectivity via `WinInet`
2. For each recipient in `lstdestinos`, the code configures `TIdMessage` with the HTML body, sender info, and subject
3. `TIdSMTP` authenticates (`atLogin`) and connects to the configured host/port
4. The message is sent and the connection is disconnected per recipient
5. Progress is tracked in the `TStatusBar` and `TProgressBar`

## Installation

1. Open `EM.dpr` in Borland Delphi 7 (or a compatible IDE such as Embarcadero RAD Studio)
2. Ensure Indy 9 components are installed in the IDE
3. Compile the project (Ctrl+F9) or build and run (F9)

## Usage

1. Launch the compiled `EM.exe`
2. Enter the SMTP server details (host, port, username, password)
3. Set the sender email address and display name
4. Compose the message body (supports HTML markup)
5. Add recipients manually or import a text file (one email per line)
6. Click **Enviar** to send to all recipients
7. Use **Visualizar HTML** to preview the message rendering

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).
