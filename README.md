# KeyLab Suite

KeyLab is a multi-screen Keynote orchestration system for macOS. It renders `.key` files natively on display Macs and provides wireless control from a central operator station — no cloud services, third-party dependencies, or internet connection required.

## The Suite

| App | Description |
|-----|-------------|
| **KeyLab Player** | Renders Keynote presentations on display Macs. Supports transitions, embedded video, display grading, visual effects, and logo overlays. |
| **KeyLabRemote** | Controller app (macOS & iPad) for managing a fleet of Players — grouping, slide control, scheduling, and remote maintenance. |
| **KeyLabServer** | Coordinator service for health monitoring and group sync across multiple Remotes. |

## Download

Download the latest installers from the [Releases](https://github.com/manofsciencestudios/Keylab-Releases/releases) page:

| File | What It Installs |
|------|-----------------|
| `KeyLabRemote-x.x.zip` | KeyLabRemote controller app (macOS). Unzip and drag to Applications. |
| `KeyLabPlayer.pkg` | KeyLab Player installer (macOS). Double-click to install to /Applications. |
| `KeyLabServer.pkg` | KeyLab Server installer (macOS). Double-click to install to /Applications. |

### Auto-Updates (KeyLabRemote)

KeyLabRemote checks for updates automatically via Sparkle. You'll be prompted when a new version is available, or check manually via **KeyLabRemote > Check for Updates...** in the menu bar.

## System Requirements

- macOS 14.0 (Sonoma) or later
- Local network (Wi-Fi or Ethernet) connecting all machines
- iPad support: iPadOS 17.0 or later (KeyLabRemote only)

## Documentation

- [USER_MANUAL.md](USER_MANUAL.md) — Full user manual covering setup, features, controls, and troubleshooting
- [OSC_GLOSSARY.md](OSC_GLOSSARY.md) — OSC address reference for third-party controller integration

## Beta Testing

If you're a beta tester, please report issues to the development team. Include:
- Which app and version (visible in the app's About window)
- Steps to reproduce the issue
- macOS version and hardware
