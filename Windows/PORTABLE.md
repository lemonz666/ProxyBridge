# Windows portable ZIP

The Windows CI workflow produces `ProxyBridge-Windows-x64-Portable.zip` alongside the normal NSIS installer.

## Usage

1. Extract the ZIP into a writable directory (not `Program Files`).
2. Run `ProxyBridge.exe`; approve the Windows administrator/UAC prompt.
3. The `portable.flag` file next to the executable forces the GUI to load `ProxyBridgeCore.dll` directly, even if a system ProxyBridge service is installed.
4. Settings and profiles are stored under `data/settings.json` and `data/profiles/*.pbprofile` inside the extracted folder.

Do not remove `portable.flag`. Keep the WinDivert DLL and SYS file beside the executable. A Windows administrator is still required to load the WinDivert kernel driver. Portable mode does **not** mean driverless operation.

The existing installer build remains unchanged, retains service-based operation, and stores its configuration under `%APPDATA%\ProxyBridge`. Portable profiles are separate: export/import profiles when migrating.

## Validation checklist

- Build ZIP from the Windows CI artifact; verify ZIP has the five required binaries and `portable.flag`.
- Extract on a clean Windows 10/11 x64 VM without the service installed; verify UAC prompt, launch, TCP/UDP routing, and shutdown.
- Reopen; verify settings and profiles persist in `data/` and not `%APPDATA%`.
- Test with an already installed ProxyBridge service; verify the portable GUI bypasses it and requests elevation.
- Verify no permanent `ProxyBridgeSvc` service is created. Inspect transient WinDivert driver state separately.
- Verify normal NSIS installation and service mode still operate when no `portable.flag` is present.

Until those smoke tests pass, the ZIP should be treated as an unsigned development artifact, not a production release.
