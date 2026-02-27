# Contributing

> **This project was discontinued in December 2013 and is no longer actively maintained.**
> The repository is preserved as a historical reference. No new features or bug fixes are planned.

## Historical Build Information

This project was built using the following tools and technologies:

- **Language:** Object Pascal (Delphi 7)
- **IDE:** Borland Delphi 7
- **Libraries:** Indy 9 (`TIdSMTP`, `TIdMessage`, `TIdAttachment`, `TIdSSLIOHandlerSocket`)
- **SSL:** OpenSSL (`libeay32.dll`, `ssleay32.dll`) embedded as Win32 resources
- **HTML Preview:** `TWebBrowser` (OLE/COM Internet Explorer control)

### Build Steps (Historical)

1. Open `EM.dpr` in Borland Delphi 7 (or compatible IDE such as Embarcadero RAD Studio)
2. Ensure Indy 9 components are installed in the IDE
3. Compile the project (`Ctrl+F9`) or build and run (`F9`)

> **Note:** `Clear.bat` removes Delphi temporary files (`.dcu`, `.dsk`, `.cfg`, etc.).
