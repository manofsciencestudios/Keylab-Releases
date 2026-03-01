# KeyLab User Manual — 2026-02-15

---

## Table of Contents

1. [Introduction & System Overview](#1-introduction--system-overview)
2. [System Requirements](#2-system-requirements)
3. [Getting Started](#3-getting-started)
4. [App-by-App Feature Guide](#4-app-by-app-feature-guide)
   - [4A. KeyLab (Player)](#4a-keylab-player)
   - [4B. KeyLabRemote (Controller)](#4b-keylabremote-controller)
   - [4C. KeyLabServer (Server)](#4c-keylabserver)
   - [4D. KeyShuffler](#4d-keyshuffler)
5. [Controls Reference](#5-controls-reference)
6. [Best Practices](#6-best-practices)
7. [Maintenance Tools](#7-maintenance-tools)
8. [Troubleshooting](#8-troubleshooting)
9. [Glossary](#9-glossary)

---

## 1. Introduction & System Overview

KeyLab is a multi-screen Keynote presentation control system designed for live events, installations, and exhibitions. It renders `.key` files natively on macOS display Macs and provides wireless control from a central operator station. No cloud services, third-party dependencies, or internet connection is required — everything runs on your local network.

### The Four Apps

**KeyLab (Player)** renders Keynote presentations on display Macs. Each Player opens a `.key` file, displays slides with full support for text, images, shapes, transitions, and embedded video, and listens for commands from Controllers over the network. Players also support display grading (brightness, contrast, color temperature, RGB channels), visual effects overlays, logo overlays, blackout/whiteout modes, and a layer-editing mode.

**KeyLabRemote (Controller)** is the wireless command center, available for macOS and iOS/iPadOS. An operator uses the Controller to discover Players on the network, target one or many displays, navigate slides, adjust grading, send Keynote files, toggle overlays, and manage groups. In Extended Mode the Controller connects to a Server for room-based management with a 2D grid layout editor.

**KeyLabServer (Server)** is an optional centralized metadata store for large deployments. It maintains rooms, device assignments, grid positions, and synchronized state. When a Server is running, Controllers automatically discover it and enter Extended Mode, enabling multi-room management, grid-based spatial awareness, and multi-operator coordination.

**KeyShuffler** is a standalone tool for generating slide variations. It opens a `.key` file, detects grouped layers, and produces shuffled variations by toggling layer visibility — useful for creating diverse content across multiple screens from a single template.

### Architecture

```
                    ┌─────────────────────────────┐
                    │       KeyLabServer           │
                    │   (optional, port 7780)      │
                    │   _keylabserver._tcp.        │
                    └──────────┬──────────────────┘
                               │
                   ┌───────────┼───────────┐
                   │                       │
          ┌────────▼────────┐     ┌────────▼────────┐
          │  KeyLabRemote   │     │  KeyLabRemote   │
          │  (Controller)   │     │  (Controller)   │
          │  macOS / iOS    │     │  macOS / iOS    │
          └───┬───┬───┬─────┘     └────────┬────────┘
              │   │   │                    │
     ┌────────┘   │   └──────┐             │
     │            │          │             │
┌────▼────┐ ┌────▼────┐ ┌───▼─────┐ ┌────▼────┐
│ KeyLab  │ │ KeyLab  │ │ KeyLab  │ │ KeyLab  │
│ Player  │ │ Player  │ │ Player  │ │ Player  │
│ port    │ │ port    │ │ port    │ │ port    │
│ 7778    │ │ 7778    │ │ 7778    │ │ 7778    │
└─────────┘ └─────────┘ └─────────┘ └─────────┘
  _keylablite._tcp.   (Bonjour / mDNS discovery)
```

### Two Operating Modes

**Classic Mode** — Direct controller-to-player operation. No server required. The Controller discovers Players via Bonjour and sends commands directly. Best for small setups with fewer than 10 displays and a single operator.

**Extended Mode** — Server-backed operation. When a KeyLabServer is running on the network, Controllers automatically discover it and enter Extended Mode. This enables rooms, grid layouts, device assignment, spatial awareness, and multi-operator coordination. Best for large shows with 10+ displays or multiple rooms.

---

## 2. System Requirements

| Component | Requirement |
|-----------|-------------|
| **KeyLab (Player)** | macOS 12 Monterey or later |
| **KeyLabRemote (Controller)** | macOS 12 Monterey or later, or iOS 15+ / iPadOS 15+ |
| **KeyLabServer** | macOS 12 Monterey or later |
| **KeyShuffler** | macOS 12 Monterey or later |
| **Network** | All devices on the same local network (LAN). Wi-Fi or Ethernet. Same subnet required. |
| **Dependencies** | None. No third-party frameworks, no internet, no cloud services. |

---

## 3. Getting Started

### Startup Sequence

Follow these steps in order for a clean startup:

#### Step 1 — Network

Ensure all Macs and iOS devices are on the **same Wi-Fi or Ethernet LAN**. All devices must be on the same subnet. Avoid guest networks or captive-portal Wi-Fi.

#### Step 2 — (Optional) Start KeyLabServer

If using Extended Mode, launch KeyLabServer **first** on a dedicated Mac. Confirm the status bar shows the server is running. Create rooms and give the server a name by clicking the server name field.

#### Step 3 — Start KeyLab Players

Launch KeyLab on each display Mac. On first launch, **approve the macOS "Local Network" permission prompt** — this is required for Bonjour discovery. Open a `.key` file via drag-and-drop or File > Open. The toolbar will show the connection count (starts at 0, meaning the Player is ready and listening).

#### Step 4 — Start KeyLabRemote

Launch KeyLabRemote on the operator's Mac or iPad. Players appear automatically in the player list within a few seconds via Bonjour discovery. If a Server is running, the Controller auto-discovers it and enters Extended Mode — the Server Status Bar at the bottom will show the connection.

#### Step 5 — Select Targets

Tap or click players in the list to select which displays to control. Use the **All** button in the header to target every player at once, or Cmd+Click / Shift+Click (macOS) to multi-select.

#### Step 6 — Send a Keynote

Use the context menu on a player (right-click or long-press) to send a `.key` file. In Extended Mode, you can also use the Server menu to distribute files.

#### Step 7 — Navigate

Use the **Previous / Next** buttons in the Controls section, or press **Cmd+Left / Cmd+Right** (Controller) or **Left / Right arrow keys** (Player) to drive the presentation.

### First-Launch Checklist

- [ ] macOS **Local Network** permission approved on each Player (System Settings > Privacy & Security > Local Network)
- [ ] macOS **Firewall** allows incoming connections for KeyLab (port 7778) and KeyLabServer (port 7780) — System Settings > Network > Firewall
- [ ] macOS **Accessibility** permission granted to KeyLab on each Player (System Settings > Privacy & Security > Accessibility) — enables password-free remote reboot
- [ ] Displays arranged and named in System Settings > Displays
- [ ] All devices confirmed on the same network subnet

---

## 4. App-by-App Feature Guide

### 4A. KeyLab (Player)

The Player app renders Keynote presentations on display Macs and responds to commands from Controllers.

#### Main Window — Presentation Canvas

The main window consists of a toolbar (hidden in fullscreen) and a full-screen canvas that renders the current slide. The canvas supports:

- **Full Keynote slide rendering** — text, images, shapes, transitions, embedded video
- **VFX overlay** — colored marker shapes (plus, circle, square) at the four corners and center, with configurable background and marker colors
- **Logo overlay** — custom PNG image with configurable background color, sizing, and optional DVD-style bouncing animation
- **Brightness dimming overlay** — transparent black layer controlled by the brightness slider
- **Blackout / Whiteout** — full-screen black or white overlays that hide the presentation entirely
- **Name overlay** — displays the Mac's hostname plus current grading values (brightness, color temperature, contrast) in large text at the bottom-right corner
- **Message overlay** — displays large centered text sent from a Controller
- **Edit mode** — layer inspector with selection handles for repositioning and scaling individual slide elements

#### Toolbar

The toolbar appears at the top of the window (hidden in fullscreen mode):

| Control | Description |
|---------|-------------|
| **Previous / Next** | Navigate between slides |
| **Slide counter** | Shows current slide / total (e.g., "3/12"). Shows VFX preset name when VFX is active. |
| **Blackout** | Toggle full-screen black overlay. Turns red when active. |
| **Brightness slider** | 0–100%. Controls the dim overlay. |
| **Color Temperature slider** | Warm/Cool color shift. Neutral at center (6500K / D65 daylight), warm to the left (down to 2000K — amber/orange tint), cool to the right (up to 9500K — blue tint). Uses Apple's CITemperatureAndTint filter with mired-space mirroring for perceptually accurate color shifts. |
| **Contrast slider** | 0.5x–2.0x. Default 1.0x. |
| **Reset Grading** | Resets brightness to 100%, color temp to neutral (6500K), and contrast to 1.0x. Disabled when already at defaults. |
| **Edit Mode toggle** | Lock/unlock icon. Orange when in edit mode. Enables layer selection and manipulation. |
| **Connection count** | Shows number of connected Controllers (green antenna icon + count). |
| **Name overlay toggle** | Shows/hides the hostname and grading info overlay. |

#### File Menu

| Item | Description |
|------|-------------|
| **Open...** | Open a `.key` file |
| **Open Recent** | Reopen a recently opened file |
| **Show in Finder** | Reveals the currently loaded `.key` file in Finder. If no keynote is loaded, opens the `Active Show` directory in `~/Documents/KeyLab/`. |
| **Close** | Close the current window |

#### Keyboard Shortcuts — KeyLab Player

| Shortcut | Action |
|----------|--------|
| `→` or `Space` | Next slide / next build step |
| `←` | Previous slide (on slide 1: reset and pause videos) |
| `Cmd+Shift+B` | Toggle blackout |
| `Cmd+B` | Exit blackout (when blacked out) |
| `Cmd+Shift+W` | Toggle whiteout |
| `Cmd+E` | Toggle edit mode |
| `Cmd+Ctrl+F` | Enter fullscreen |
| `Esc` | Exit fullscreen |
| `J` | Video: slow down (2x → 1.5x → 1x → 0.5x → pause) |
| `K` | Video: play / pause toggle |
| `L` | Video: speed up (pause → 1x → 1.5x → 2x) |
| `Cmd+O` | Open file |
| `Cmd+W` | Close window |
| `Cmd+Q` | Quit |

#### Multi-Screen Mode

KeyLab supports multi-screen output when additional displays are connected. Each screen operates independently with its own slide position, grading (brightness, contrast, color temperature, RGB), overlays (blackout, whiteout, VFX, logo, message, test pattern), and video playback. M4 Mac minis support up to 3 displays, and Mac Studios support up to 8.

**How it works:**
- When additional displays are detected, the Player automatically creates a fullscreen window on each extra screen.
- Commands from the Controller include an optional `displayIndex` parameter (0 = primary, 1 = second screen, 2 = third screen, etc.). Commands without a `displayIndex` target the primary screen.
- Each screen's state (slide, grading, blackout) is persisted independently and restored on relaunch.
- All extra screens support the same overlays as the primary: blackout, whiteout, name overlay, message overlay, VFX, logo, and test patterns.
- Multi-screen mode can be enabled/disabled remotely from the Controller. The preference persists across restarts.

**Behavior notes:**
- If a display is disconnected, its window is destroyed and the remaining screens continue operating.
- If multi-screen mode is disabled via the Controller, all secondary windows close even if extra displays are present.
- Video playback is independent per screen — each screen can be at a different slide with different video state.
- Hot-plugging displays is supported: connecting or disconnecting a display automatically creates or destroys the corresponding window.

---

### 4B. KeyLabRemote (Controller)

The Controller app provides the operator interface for commanding one or many Players. It runs on macOS, iPad, and iPhone, with platform-appropriate layouts for each.

#### Platform Layouts

**macOS** — Single-column window: header bar, player list, controls, server status bar. Additional windows for Room Grid, Network, Settings, Message, and Logs.

**iPad** — Tabbed full-width layout with two tabs: **Players** (player list) and **Room Grid** (2D grid canvas). The layout adapts to orientation:

- **Portrait**: Header → tab bar → full-width tab content → controls → status bar (all stacked vertically)
- **Landscape**: Header → tab bar → then a side-by-side split: tab content on the left with a 380pt controls sidebar on the right

Controls are always visible in both orientations. The room picker is accessed via the gearshape (settings) menu rather than an inline tab bar. Player groups synced from the Server appear automatically on iPad.

**iPhone** — Single-column layout: header, player list, controls, status bar. No grid tab (iPhone screen is too small for room grid interaction). All player management features work identically to iPad's Players tab.

#### Header Bar

| Control | Description |
|---------|-------------|
| **All** | Select all players as targets. A blue border appears around the entire window when active. |
| **Clear** | Deselect all players. |
| **Remove Offline** | Remove stale/disconnected player entries from the list. |
| **Search field** (`Cmd+F`) | Filter players by name. |
| **Interface lock toggle** | Locks the entire interface. A full-screen overlay prevents accidental input. Tap the lock overlay to unlock. |
| **Settings** | Open the Settings window (macOS) or sheet (iOS). On iPad, settings and room selection are combined in the gearshape menu. |
| **Logs** | Open the log viewer. |
| **Network Window** | (macOS only) Open the Network Window listing all machines discovered on the LAN — KeyLab players, SSH hosts, and VNC (Screen Sharing) hosts. |
| **Room Grid** (`Option+Cmd+G`) | (macOS, Extended Mode) Open the 2D room layout editor. On iPad, the grid is the second tab. |

#### Player List

The scrollable center area displays discovered players organized into groups:

- **Hierarchical groups** — expandable sections with color indicators
- **Player cards** show: status dot (green = online, yellow = degraded, red = offline, gray = unknown), name, room assignment, current filename, slide number, build progress, blackout badge, and optional slide thumbnail. Thumbnails reflect the player's current grading (brightness, contrast, color temperature, RGB levels) via lightweight SwiftUI modifiers. A small red **LIVE** badge appears on the thumbnail when live network pull mode is active.
- **Drag-to-reorder** within groups
- **Context menu** per player (right-click or long-press): fullscreen, send keynote, recall from archive, assign to room, unassign, group management, thumbnail mode

#### Controls View

The bottom section provides the main operator controls:

| Control | Description |
|---------|-------------|
| **Previous / Next** | Navigate slides on all targeted players |
| **Slide counter** | Shows current slide position. Click to open the Slide Jumper modal for direct slide navigation. |
| **Build progress dots** | Visual indicator of build steps within the current slide |
| **Slide 1** | Jump all targeted players to the first slide |
| **Blackout** | Toggle blackout on all targeted players |
| **Shuffle** | Randomize slide order across targeted players |
| **VFX** | Toggle VFX overlay on targeted players |
| **Logo** | Toggle logo overlay on targeted players |
| **Message** | Open the Message window to send text to player screens |
| **Brightness slider** | Adjust brightness on targeted players (0–100%) |
| **Contrast slider** | Adjust contrast on targeted players (0.5x–2.0x) |
| **Color Temperature slider** | Adjust color temperature on targeted players. Display shows Warm/Cool percentage or raw Kelvin value depending on Settings. Neutral = 6500K (no shift). Lower values warm the image (amber/orange), higher values cool it (blue). |
| **RGB sliders** | (collapsible) Individual Red, Green, Blue channel level adjustments |

**Per-screen targeting:** When controlling a player with multi-screen enabled, commands can target a specific screen. The `displayIndex` field in each command determines which screen receives it (0 = primary, 1 = second screen, 2 = third screen, etc.). Commands sent without a screen target default to the primary screen. Player tiles in the Controller show screen badges ("Scr1", "Scr2", "Scr3", etc.) when the player has multiple screens.

#### Server Status Bar

The bottom edge shows:
- Server connection indicator (connected/disconnected)
- Server name
- Current room info
- Online/offline device counts

#### Additional Windows (macOS)

**Network Window** — A comprehensive window listing every machine discovered on the local network. The list merges multiple discovery sources:

- **KeyLab Players** — discovered via the `_keylablite._tcp.` Bonjour service, shown with a live health status dot (green/yellow/red)
- **SSH hosts** — discovered via the `_ssh._tcp.` Bonjour service, shown with an orange terminal icon
- **VNC hosts** — discovered via the `_rfb._tcp.` (Screen Sharing) Bonjour service, shown with a blue display icon
- **Offline devices** — players assigned to the server that are not currently on the network, shown with a gray dot, dimmed hostname, and "Offline" in the IP column

Machines are merged by hostname — a single machine may appear with multiple service icons if it advertises SSH, VNC, and is also a KeyLab player.

**Table columns:** Status, Hostname, Screen #, Services (icons), Version, Server, Room, IP, Keep-Alive, Screens

**Group organization:** If you have created player groups in the sidebar, the Network Window displays machines organized by those groups with collapsible section headers. Ungrouped machines appear at the bottom. Non-player machines (SSH/VNC only) always appear in the Ungrouped section.

**Per-screen grouping:** When using per-screen groups (where individual screens of a multi-screen player are assigned to different groups), the Network Window correctly resolves screen-qualified group members to their parent device. The **Screen #** column shows which screen number(s) each device contributes to that group (1-based, e.g., "1" or "1, 2"). A device with screens in multiple groups will appear in each group's section, with the Screen # column indicating which screen(s) belong to that group. Ungrouped and offline devices show a dash in this column.

**Player version:** The Version column shows the KeyLab app version (e.g. "1.2.3") for connected players.

**Screen Share:** Right-click any machine with VNC available and choose "Screen Share" to open macOS Screen Sharing. Only one Screen Sharing session at a time is permitted — close the existing session before opening a new one. Screen Sharing automatically opens in standard (lower) quality mode for faster response during rapid pop-in/pop-out setup sessions.

**SSH Operations:** For machines with SSH enabled, you can install software packages, deploy apps, and launch KeyLab remotely:

- **Install Package** — Select one or more SSH-capable machines, then choose "Install Package" from the SSH toolbar menu or context menu. Pick a `.pkg` file — it will be copied via SCP and installed with `sudo installer` on all selected machines **in parallel**. A single summary alert reports the outcome across all machines (succeeded, partially succeeded, or failed with per-machine error details).
- **Deploy Player** — Deploys the bundled KeyLab Player `.pkg` to selected machines via SCP + SSH. The `.pkg` is installed with `sudo installer`, then the Player LaunchAgent is restarted (`launchctl bootout` + `bootstrap`) so the new version launches immediately. Only appears when a Player `.pkg` is embedded in the Remote build.
- **Deploy Server** — Deploys the bundled KeyLab Server `.pkg` to selected machines via SCP + SSH. The `.pkg` is installed with `sudo installer`. No LaunchAgent restart is performed (Server is a GUI app, not a daemon). Only appears when a Server `.pkg` is embedded in the Remote build.
- **Launch Player** — Select non-player machines with SSH and choose "Launch Player" to remotely open `KeyLab.app` via SSH.

All SSH operations use the fleet SSH credentials configured in Settings (see below). If no credentials are saved, you will be prompted for a username and password, with the option to save them as the default.

**Selection and bulk actions:**
- Click, Cmd+Click, Shift+Click, or Cmd+A to select one or more rows
- A toolbar row appears with menus for: Player (Reboot, Force Quit, Relaunch, Keep-Alive, Deploy Update), Network (Set Network Config, Rename Hostname, Cycle Wi-Fi, Join Wi-Fi, Wi-Fi On/Off), SSH (Screen Share, Launch Player, Install Package, Deploy Player, Deploy Server), Content (Send Keynote, Recall from Archive, Enter/Exit Fullscreen)
- A footer action bar appears with two buttons:
  - **Unassign from Room** — removes the selected players' room assignments (Extended Mode only)
  - **Unassign from Server** — removes the selected players from the server roster entirely (requires confirmation)
- Right-click any row for Screen Share, maintenance actions, and unassign options via context menu

**Room Grid** (Extended Mode, `Option+Cmd+G` on macOS / Grid tab on iPad) — A 2D room layout editor with:
- Device tiles showing player positions within the room
- Wall and door drawing tools
- Shape palette (rectangle, circle, right triangle, triangle, text label)
- Element inspector for transforms and properties (scrollable on iPad)
- Pan, zoom, and marquee selection
- Tool palette with keyboard shortcuts
- Drag-and-drop: on iPad, drag unassigned players from the "Not on Grid" tray onto the canvas to place them in the room

**Room Selection** — On macOS, rooms are shown as tabs above the grid canvas. On iPad, rooms are selected via the gearshape menu: tap the gear icon and choose a room from the "Room" section. A checkmark indicates the currently selected room.

**Group Sync** — Player groups are synchronized across all connected Remotes via the Server. When groups are created or renamed on any Controller (macOS or iPad), the change is broadcast to all other connected Controllers within seconds. Groups appear identically in the player list on every device.

**Settings** — The Settings window (macOS) is organized into three tabs. On iOS, only the General tab content is shown.

**General tab** — Display and behavior preferences:

| Setting | Default | Range / Options |
|---------|---------|-----------------|
| Color Temp Display Mode | Percent | Percent, Kelvin |
| Logo Image | Default logo | File picker (macOS) |
| Logo Background Color | Black (0,0,0,1) | RGBA color picker |
| Logo Bouncing | Disabled | On/Off |
| Live Slider Updates | Enabled | On/Off |
| Live Update Min Interval | 0.1 seconds | 0.05–1.0 seconds |
| Live Update Step | 2% | 1–10% |
| Blackout Fade Duration | 0.0 seconds | 0–1.0 seconds |
| Shuffle Avoid Repeats | Enabled | On/Off |
| Thumbnails | Enabled | On/Off — when enabled, slide thumbnails appear on player cards. Thumbnails are sourced locally from the .key file when available (zero network cost) or pulled live from the player. |
| VFX Shape | Plus | Off, Plus, Circle, Square |
| VFX Color Presets | Default presets | Custom preset editor |

**Communications tab** (macOS only) — Network and connection settings:

| Setting | Default | Range / Options |
|---------|---------|-----------------|
| Offline Timeout | 15 seconds | 5–60 seconds |
| Discovery Mode | Bonjour Only | Bonjour Only, Bonjour + Server, Bonjour + Static IPs |
| Fleet Credentials (SSH) | — | Username + password (Keychain) |
| Fleet Credentials (Wi-Fi) | — | SSID + password (Keychain) |
| OSC Enable | Disabled | On/Off |
| OSC UDP Port | 8000 | 1–65535 |
| OSC Feedback | Disabled | On/Off + destination list (host:port) |

- **SSH credentials** are used by Install Package and Launch Player operations. If not configured, you will be prompted when running an SSH operation, with the option to save as default.
- **Wi-Fi credentials** pre-fill the "Join Wi-Fi Network" dialog, saving time when configuring multiple players to join the same network.
- Use the **Clear** button next to each section to remove stored credentials.
- A green checkmark appears when credentials are fully configured (both username/SSID and password).

**Test Patterns tab** (macOS only) — Test pattern defaults and custom pattern management. Settings are arranged in a two-column layout with the following cards:

| Card | Contents |
|------|----------|
| Default IRE Level | 75% / 100% / 109% segmented picker |
| Grid Defaults | Line color, background color, diagonal lines, circles, labels |
| Sync Frame Rate | Frame rate picker for Audio Sync timecode (23.976–60 fps) |
| LED Wall Defaults | Panel width/height, columns/rows, border, position, Color A/B |
| Custom Patterns | Up to 4 PNG slots with thumbnails — full width at the bottom |

Custom pattern slots show a 48×48 thumbnail preview when a PNG is loaded, with the filename and a Clear button. Empty slots show a placeholder icon with an Import button.

**Keyboard Shortcuts** (`F1`) — Reference card showing all grid and playback shortcuts.

**Message Window** — Send text messages to display on player screens.

**Log Window** — Real-time activity and debug log viewer.

#### Keyboard Shortcuts — KeyLabRemote

**Playback & Navigation:**

| Shortcut | Action |
|----------|--------|
| `Cmd+←` | Previous slide |
| `Cmd+→` | Next slide |
| `J` | Video: slow down |
| `K` | Video: play / pause |
| `L` | Video: speed up |

**Actions:**

| Shortcut | Action |
|----------|--------|
| `Cmd+J` | Jump to slide (opens slide picker) |
| `Cmd+]` | Brightness up (+5%) |
| `Cmd+[` | Brightness down (-5%) |
| `Cmd+L` | Toggle interface lock |

**Windows:**

| Shortcut | Action |
|----------|--------|
| `Option+Cmd+G` | Open / close Room Grid |
| `Option+Cmd+N` | Open Network window |
| `Option+Cmd+S` | Screen Share (single selected player) |
| `Option+Cmd+L` | Open Logs window |

**App Controls:**

| Shortcut | Action |
|----------|--------|
| `Cmd+A` | Select all players |
| `Cmd+F` | Focus search field |
| `F1` | Keyboard shortcuts reference |
| `Esc` | Clear search / reset tool |
| `Cmd+N` | New config |
| `Cmd+O` | Open config |
| `Cmd+S` | Save config |
| `Cmd+Shift+S` | Save config as |
| `Option+Cmd+E` | Export snapshot |

**Grid Editing — Tools:**

| Shortcut | Tool |
|----------|------|
| `Cmd+1` | Select (pointer) |
| `Cmd+2` | Move |
| `Cmd+3` | Zoom |
| `Cmd+4` | Wall (edit mode) |
| `Cmd+5` | Door (edit mode) |

**Grid Editing — Add Shapes (edit mode):**

| Shortcut | Shape |
|----------|-------|
| `Option+1` | Rectangle |
| `Option+2` | Circle |
| `Option+3` | Right Triangle |
| `Option+4` | Triangle |
| `Option+5` | Text Label |

**Grid Editing — Actions:**

| Shortcut | Action |
|----------|--------|
| `Cmd+E` | Edit layout / Save & exit |
| `Cmd+Delete` | Delete selected element |
| `Cmd++` | Zoom in |
| `Cmd+-` | Zoom out |
| `Arrow keys` | Nudge selection (0.25 units; hold Shift for 1.0 units) |
| `Esc` | Reset tool to pointer |

---

### 4C. KeyLabServer

The Server app provides centralized state management for large deployments.

#### Dashboard Window

**Server Header:**
- **Server name** — click to edit
- **Room picker** — dropdown to switch between rooms, plus a "New Room" button to create additional rooms

**Two View Modes:**
- **List View** — device table with columns for health status, hostname, group, room, grid position, keynote filename, and slide number
- **Grid View** — 2D canvas showing device tiles at their assigned physical positions within the room, along with room elements (walls, doors, shapes)

**Device Table:**
- Health indicator dot (online/offline)
- Slide status (current slide number)
- Grading status

**Log Panel** — collapsible panel at the bottom showing real-time server activity logs

**Status Bar** — bottom edge showing server state, connected controller count, and device health summary

#### Menu

| Menu Item | Action |
|-----------|--------|
| File > Export State | Export the current server state as a JSON file |
| File > Import State | Import a previously exported server state |
| Server > Unassign All Players | Remove all player assignments (requires double confirmation) |
| Window > Show Log | Toggle the log panel |

#### Keyboard Shortcuts — KeyLabServer

| Shortcut | Action |
|----------|--------|
| `Cmd+Shift+C` | Copy all logs |
| `Cmd+K` | Clear logs |

---

### 4D. KeyShuffler

KeyShuffler generates presentation variations by shuffling layer visibility.

#### Main Window

**Left Panel — Preview:**
- Live slide preview showing the current variation
- Navigation controls to cycle through preview variations

**Right Sidebar:**
- **Variety Picker** — Low / Medium / High randomization level:
  - **Low**: 1–2 element changes per variation (minimal difference)
  - **Medium**: 3–5 element changes (balanced diversity)
  - **High**: Maximum diversity (fewer total variations possible)
- **Output Picker** — select the export directory for generated files
- **Element Groups** — auto-detected groups (wand icon) or manually created. Each group controls visibility of its member layers.
- **Generate Button** — produces all variations with a progress indicator
- **Variation Estimate** — shows the estimated number of unique variations possible given the current group configuration

#### Workflow

1. Open a `.key` file (supports both iCloud `.key` and local `.sffkey` formats)
2. Review auto-detected element groups or create custom ones
3. Choose a variety level (Low / Medium / High)
4. Select an output directory
5. Preview example variations
6. Click **Generate** to produce all variation files

#### Settings Window

- Keynote automation permission status indicator
- Request Permission button (required for Keynote interaction)
- Link to System Settings for manual permission management

---

## 5. Controls Reference

A consolidated reference of all controls across the KeyLab system.

### Navigation

| Control | Where | Description |
|---------|-------|-------------|
| Previous Slide | Player, Controller | Go to the previous slide |
| Next Slide | Player, Controller | Go to the next slide or trigger next build step |
| Slide 1 | Controller | Jump to the first slide |
| Slide Jumper | Controller | Click the slide counter to open a direct slide picker |
| `→` / `Space` | Player | Next slide / build |
| `←` | Player | Previous slide |
| `Cmd+→` | Controller | Next slide |
| `Cmd+←` | Controller | Previous slide |

### Video Playback

| Control | Key | Description |
|---------|-----|-------------|
| Slow Down | `J` | 2x → 1.5x → 1x → 0.5x → Pause |
| Play / Pause | `K` | Toggle video playback |
| Speed Up | `L` | Pause → 1x → 1.5x → 2x |

### Display Overlays

| Control | Where | Description |
|---------|-------|-------------|
| Blackout | Player toolbar, Controller | Full black overlay hiding the presentation (toggle) |
| Whiteout | Player (`Cmd+Shift+W`) | Full white overlay |
| Name Overlay | Player toolbar | Show hostname + current grading values |
| Logo | Controller | Toggle logo image overlay on players |
| VFX | Controller | Toggle visual effects markers overlay |
| Message | Controller | Send text message to display on player screens |
| Test Pattern | Controller | Toggle test pattern overlay on players |

### Test Patterns

Test patterns are full-screen overlays rendered natively on the player. They are used for display alignment, color calibration, and system diagnostics. Test patterns are managed from the **Overlays** menu in the Controller app.

#### Built-in Patterns

| Pattern | Description |
|---------|-------------|
| Solid White/Black/Gray/Red/Green/Blue | Flat solid color fills |
| Simple Bars | 8 vertical color bars (White, Yellow, Cyan, Green, Magenta, Red, Blue, Black) at configurable IRE levels |
| SMPTE Color Bars | Standard SMPTE/EBU 75% color bars with animated scan line and PLUGE |
| ARIB Bars | ARIB multi-row broadcast standard with 100%/75% bars, luminance ladder, gradient ramp, and Rec.709 colors |
| HDR Bars | HDR extended range test with sharpness squares, stripe patterns, and rainbow gradient |
| SDI Pathological | SDI pathological stress test (bright magenta over mid-grey split) |
| Grayscale Ramp | Smooth or stepped gradient with configurable direction, double (mirror), and reverse |
| Crosshatch Grid | Broadcast-reference grid with 32px cells (bold at 128px), pixel-coordinate edge labels, inscribed circle with corner arcs, and center info box showing hostname + resolution |
| Full HDTV | HD test card image (letterboxed to 16:9) |
| Custom 1–4 | User-uploaded PNG images (configured in Settings) |

#### IRE Levels

The **IRE Level** submenu in the Overlays menu controls the peak brightness of the Simple Bars and SMPTE patterns:

| Level | Use Case |
|-------|----------|
| 75% (Standard) | Default broadcast standard. Colors are at 75% of full intensity. |
| 100% | Full-range digital. Bars are at 100% intensity. |
| 109% (Super-white) | Extended range. Tests display headroom above 100%. |

The default IRE level can be set in **Settings > Test Patterns > Default IRE Level**.

#### Grid Customization

The Crosshatch Grid is a broadcast-reference pattern with fixed 32px cells, bold emphasis at 128px multiples, and pixel-coordinate labels counting from center. It is configurable via **Settings > Test Patterns > Grid Defaults**:

| Setting | Range | Default | Description |
|---------|-------|---------|-------------|
| Line color | Any color | 50% Gray | Color of grid lines, diagonals, circles, labels, and info box |
| Background | Any color | Black | Background fill color |
| Diagonal lines | On/Off | On | Corner-to-corner diagonal lines |
| Inscribed circle & corner arcs | On/Off | On | Full inscribed circle centered on screen, plus quarter-arc at each corner |
| Pixel coordinate labels | On/Off | On | Pixel distance from center at every 32px gridline along all four edges |

The center info box always shows the hostname and resolution (e.g. "Stage\n1920×1080"). Grid settings update live on connected players.

#### Ramp Controls

The Grayscale Ramp pattern supports configurable direction, mirroring, and reversal via the **Overlays** menu:

| Control | Description |
|---------|-------------|
| Ramp Direction | Gradient direction: Horizontal (default), Vertical, Diagonal, or Radial |
| Ramp Double | Mirror/split the gradient (top/bottom for horizontal, left/right for vertical) |
| Ramp Reverse | Flip the gradient direction (white-to-black instead of black-to-white) |

These controls combine freely: e.g. Stepped + Radial produces concentric stepped rings, Double + Reverse + Diagonal produces a mirrored diagonal gradient flipped in both halves.

#### Overlay Toggles

| Toggle | Shortcut | Description |
|--------|----------|-------------|
| Test Pattern | `Option+Cmd+T` | Enable/disable the active test pattern |
| Details Overlay | `Option+Cmd+D` | Show color names and IRE percentages on bars/ramp steps |
| Info Overlay | `Option+Cmd+I` | Show machine hostname, screen resolution, and clock centered on the pattern |

The **Details Overlay** is useful for identifying individual bars and verifying IRE levels. The **Info Overlay** helps identify specific machines in a fleet and can be used on top of any pattern.

#### Audio Sync Pattern

The **Audio Sync** test pattern displays a real-time visual timecode counter with frame-locked rendering:

- **Timecode display** — Large `HH:MM:SS:FF` counter (or `HH;MM;SS;FF` for drop-frame rates)
- **Flash bar** — White bar flashes at the top of the screen on frame 0 of each second
- **Frame progress bar** — Horizontal bar showing the current frame position within the second
- **Audio beep** — 1kHz tone (30ms) on frame 0 of each second (toggle via Overlays > Sync Audio)

**Sync Frame Rate** (Overlays menu or Settings) controls the timecode rate:

| Rate | Drop-Frame | Use Case |
|------|-----------|----------|
| 23.976 fps | No | Film pulldown |
| 24 fps | No | Cinema |
| 25 fps | No | PAL broadcast |
| 29.97 fps | Yes | NTSC broadcast (default) |
| 30 fps | No | Non-drop NTSC |
| 50 fps | No | PAL high frame rate |
| 59.94 fps | Yes | NTSC high frame rate |
| 60 fps | No | Non-drop high frame rate |

Drop-frame timecode skips frame numbers 0 and 1 at the start of each minute (except every 10th minute) to keep timecode aligned with wall-clock time at fractional rates.

#### LED Wall Pattern

The **LED Wall** test pattern renders a pixel-accurate checkerboard grid for verifying physical tile mapping on LED video walls:

- **Checkerboard** — Two alternating colors in a checkerboard pattern, configurable via Color A and Color B pickers
- **Tile sizing** — Panel Width and Panel Height set exact pixel dimensions for each tile (16–1024, step 16)
- **Grid layout** — Configurable Columns × Rows (1–50 each)
- **Border** — Toggle 1px white lines between tiles to verify alignment
- **Position labels** — Toggle `↓row →col` labels (1-based) on each tile to identify physical tile mapping
- **Resolution readout** — Settings shows computed resolution: `tileWidth × cols` by `tileHeight × rows`

Default layout is 6×4 (128×128 px tiles). Adjust in **Settings > Test Patterns > LED Wall Defaults**.

#### Pattern Cycling

When a test pattern is active, the **Left/Right arrow keys** cycle through available patterns (including custom slots).

#### Custom Test Patterns

Up to 4 custom PNG images can be uploaded in **Settings > Test Patterns**. These are distributed to all connected players and cached locally. Custom patterns stretch to fill the player screen.

### Grading

| Control | Range | Default | Description |
|---------|-------|---------|-------------|
| Brightness | 0–100% | 100% | Screen brightness (dimming overlay) |
| Contrast | 0.5x–2.0x | 1.0x | Display contrast |
| Color Temperature | 2000K–9500K | 6500K (Neutral / D65 daylight) | Warm (low K, amber/orange) to Cool (high K, blue). Applied via CITemperatureAndTint with mired-space mirroring for perceptually accurate shifts across the range. |
| Red Level | 0–1.0 | 1.0 | Red channel multiplier |
| Green Level | 0–1.0 | 1.0 | Green channel multiplier |
| Blue Level | 0–1.0 | 1.0 | Blue channel multiplier |
| Reset Grading | — | — | Returns all grading to defaults |

### Thumbnail Modes

Each player can operate in one of three thumbnail modes, selectable via right-click context menu > **Thumbnail Mode**:

| Mode | Network Cost | Description |
|------|-------------|-------------|
| **Cached (Local)** | Zero | Default. Thumbnails are extracted locally from the .key file when the Remote stages it for transfer. No network traffic. Grading (brightness, contrast, color temperature, RGB) is approximated via SwiftUI view modifiers. |
| **Live (Network)** | ~50–100 KB/s per player | Original behavior. The player renders the current slide to JPEG and sends it over the network. Shows exact on-screen appearance including VFX effects and video playback frames. A red **LIVE** badge appears on the thumbnail. |
| **Off** | Zero | No thumbnails displayed. |

**When to use Live mode:**
- Verifying VFX appearance on specific players
- Checking video playback frames
- Confirming exact color grading match (cached mode approximates)
- Debugging a player that seems out of sync

**Multi-selection:** Select multiple players (Cmd+Click or Shift+Click), then right-click any selected player to toggle the mode for all selected players at once.

**Fallback behavior:** In Cached mode, if no local thumbnail is available for a deck (e.g., the player loaded the .key file directly rather than via the Remote), the system automatically falls back to network pull.

### Targeting (Controller)

| Control | Description |
|---------|-------------|
| All | Select all players as targets |
| Clear | Deselect all players |
| Remove Offline | Remove disconnected players from the list |
| Search (`Cmd+F`) | Filter players by name |
| `Cmd+A` | Select all players |
| Click | Select a single player |
| Cmd+Click | Toggle a player in the selection (macOS) |
| Shift+Click | Extend selection (macOS) |
| Per-screen | Target a specific screen on a multi-screen player (primary, secondary, tertiary, etc.) |

### Grid Editing (Controller, Extended Mode)

**Tool Palette:**

| Tool | Shortcut | Description |
|------|----------|-------------|
| Select (Pointer) | `Cmd+1` | Select and interact with elements |
| Move | `Cmd+2` | Pan the grid canvas |
| Zoom | `Cmd+3` | Zoom into a region (marquee zoom) |
| Wall | `Cmd+4` | Draw wall segments (edit mode) |
| Door | `Cmd+5` | Place doors (edit mode) |

**Shape Palette (edit mode):**

| Shape | Shortcut | Description |
|-------|----------|-------------|
| Rectangle | `Option+1` | Add a rectangle element |
| Circle | `Option+2` | Add a circle element |
| Right Triangle | `Option+3` | Add a right triangle element |
| Triangle | `Option+4` | Add an isosceles triangle element |
| Text Label | `Option+5` | Add a text label |

**Editing Actions:**

| Action | Shortcut | Description |
|--------|----------|-------------|
| Edit / Save Layout | `Cmd+E` | Toggle between edit and view mode |
| Delete Selected | `Cmd+Delete` | Remove selected element |
| Nudge | Arrow keys | Move selection 0.25 units (Shift = 1.0 units) |
| Zoom In | `Cmd++` | Zoom in on the grid |
| Zoom Out | `Cmd+-` | Zoom out on the grid |
| Reset Tool | `Esc` | Switch back to the pointer tool |

---

## 6. Best Practices

### Pre-Show Setup

- **Test all connections** before the audience arrives. Launch every Player and confirm they appear in the Controller's player list.
- **Use the Name Overlay** (Player toolbar) to identify each screen's hostname during setup — especially useful when multiple identical displays are arranged in a row.
- **Send Keynote files early**. Transfer all presentation files to Players during setup, not during the show.

### Targeting Discipline

- **Use groups** to organize players by location, role, or content. This makes it easy to target subsets.
- **Avoid "Target All" for grading adjustments** unless you want every screen to change. It's easy to accidentally alter screens that were already calibrated.
- **Lock the controls section** when not actively adjusting to prevent accidental slider changes.

### Grading Workflow

1. Set brightness, contrast, and color temperature **per group** first.
2. Fine-tune individual players as needed.
3. Use **Reset Grading** to return to defaults quickly if adjustments go wrong.
4. Adjust RGB channels last — they're for fine color matching, not broad changes.

**Color temperature tips:**
- 6500K is neutral (D65 daylight) — no color shift.
- 3200K–4500K gives a warm tungsten/halogen look (amber/orange).
- 7500K–9500K gives a cool overcast/shade look (blue).
- The slider is perceptually calibrated using the mired scale, so equal slider increments produce visually equal shifts in both the warm and cool directions.

### Thumbnail Caching

- **Leave thumbnail mode on Cached (Local)** for normal operation. When the Remote sends a .key file to players, it automatically extracts slide thumbnails locally from the file. This eliminates ~14–28 MB/s of sustained thumbnail traffic with 200+ players.
- **Use Live mode sparingly.** Switch individual players to Live mode only when you need to verify exact on-screen appearance (VFX, video frames, or precise color grading). Switch back to Cached when done.
- **Grading previews are approximate.** The cached thumbnail applies brightness, contrast, color temperature, and RGB levels as SwiftUI view modifiers. At thumbnail size (36pt), the difference from the player's CIFilter chain is invisible — but for critical color work, use Live mode on a few representative players.

### Network Reliability

- **Use wired Ethernet** for Players whenever possible. Wi-Fi is acceptable for Controllers (iPads) but wired connections are more reliable for display Macs.
- **Keep all devices on a single subnet.** KeyLab uses Bonjour (mDNS) for discovery, which does not cross subnet boundaries.
- **Avoid guest or captive-portal Wi-Fi.** These networks often isolate devices and block mDNS.
- **Minimize network congestion.** Close bandwidth-heavy apps on the same network.

### Extended Mode for Large Shows

- **Use KeyLabServer** when managing 10+ displays or multiple rooms.
- **Assign players to rooms** to organize by physical space.
- **Use the Grid View** for spatial awareness — see which screen is where in the room layout.
- **Multiple operators** can connect simultaneously in Extended Mode. Coordinate with co-operators to avoid conflicting commands.

### Multi-Screen Setup

- **Enable multi-screen mode** on players that have multiple displays connected. The player automatically detects all connected displays and creates fullscreen windows.
- **Target screens independently** when you need different content or grading on each display. Click a specific screen card in the sidebar to target just that screen.
- **Grading is per-screen** — you can set different brightness, contrast, and color temperature on each screen of the same player.
- **Test with all screens** before the show to verify each display is rendering correctly.
- Multi-screen state (slide position, grading, blackout) is **persisted per-screen** and restored on relaunch.
- **Supported hardware:** M4 Mac mini (3 screens), Mac Studio M2 Max (5 screens), Mac Studio M2 Ultra (8 screens).

### Blackout & Transitions

- **Set a blackout fade duration** (0.3–0.5s in Settings) for smooth transitions rather than hard cuts.
- Use blackout as a transition tool between sections of the presentation.

### Shuffle

- **Enable "Avoid Repeats"** in Settings to prevent the same slide appearing twice in a row when shuffling.
- Test shuffled content during rehearsal to verify visual diversity.

### Saving State

- **Export snapshots** (`Option+Cmd+E`) before the show as a recovery backup.
- **Save configs** (`Cmd+S`) for repeatable setups that can be loaded later.
- In Extended Mode, use the Server's **Export State** (File menu) to back up the entire room configuration.

### Live Slider Updates

- **Enable** live slider updates for rehearsal — see grading changes in real time as you drag sliders.
- **Consider disabling** for live shows if the network is congested, to avoid mid-drag visual artifacts on screen.

### OTA Player Updates

KeyLabRemote can push a new build of KeyLab.app to connected Players over the network — no SSH or physical access required.

**How to deploy an update:**

1. Right-click a Player (or select multiple, then right-click) and choose **Deploy Update...**.
2. Select the new `KeyLab.app` bundle from the file picker. The Controller reads the version from the app's `Info.plist`.
3. Confirm the deployment (two-step confirmation, same as Reboot).
4. The Controller stages the `.app` as a zip, starts an HTTP server, and sends the download URL to each target Player.
5. Each Player downloads, verifies (SHA-256), extracts, swaps the running app, and restarts. The LaunchAgent (`KeepAlive=true`) automatically relaunches the new version.

**Prerequisites:**

- **Keep-Alive must be enabled** on each target Player so that launchd restarts the app after the swap. If a Player doesn't have Keep-Alive enabled, the Controller automatically enables it before sending the update.
- The Player must have **write access** to its own `.app` parent directory (standard for user-level installs in `/Applications` or `~/Applications`).

**Safety:**

- A **backup** (`KeyLab.app.bak`) is preserved alongside the updated app for manual rollback via SSH if needed.
- **Version comparison** prevents accidental downgrades — the Player skips updates where the offered version is not newer than the running version. Use **Force Update** (via code) to override.
- **Concurrent updates are blocked** — the Controller only allows one update at a time.
- **Automatic timeout recovery** — if an update appears stuck (e.g., a player disconnects before confirming completion), the Controller automatically resets the update state after 120 seconds. Player disconnections during an active update are treated as successful restarts.

### App Deployment (Remote as Distribution Hub)

KeyLabRemote can serve as the single distribution point for the entire KeyLab system. When built with embedded `.pkg` files, the Remote can deploy the Player and Server apps to any SSH-capable machine discovered on the network.

**How deployment works:**

1. Select one or more SSH-capable machines in the Network Window (or use the Maintenance > Player Maintenance menu for targeted players).
2. Choose **Deploy Player** or **Deploy Server** from the SSH toolbar menu, context menu, or Player Maintenance submenu.
3. A confirmation dialog shows the package version and target machine count.
4. The `.pkg` is copied to each machine via SCP, installed with `sudo installer`, and cleaned up — all in parallel.
5. For Player deployments, the LaunchAgent is automatically restarted so the new version launches immediately.

**When are Deploy buttons visible?**

Deploy Player and Deploy Server menu items only appear when the corresponding `.pkg` files are embedded in the Remote app bundle. Developer builds without embedded packages will not show these buttons — this is by design. To create a distribution build:

```bash
./Scripts/pkg_player.sh          # Build BullpenPlayer.pkg
./Scripts/pkg_server.sh          # Build KeyLabServer.pkg
./Scripts/embed_packages.sh path/to/KeyLabRemote.app  # Embed into Remote
```

**Prerequisites:**

- Target machines must have SSH enabled (System Settings > General > Sharing > Remote Login)
- Fleet SSH credentials must be configured (Settings > Fleet Credentials) or will be prompted
- The SSH user must have `sudo` privileges for `installer`

### Remote Self-Updates (Sparkle)

KeyLabRemote (macOS) supports automatic self-updates via [Sparkle](https://sparkle-project.org). The app periodically checks for new versions and prompts the user to download and install them.

- **Check for Updates...** is available in the **KeyLabRemote** application menu (after "About KeyLabRemote").
- Automatic background checks occur once per day when enabled.
- Updates are verified with an EdDSA signature before installation.

See `docs/01_User Documentation/SPARKLE_UPDATES.md` for setup and release workflow details.

### Locking the Interface

- **Lock the control section** (bottom section lock) when handing the controller to a less experienced operator — prevents accidental grading changes.
- **Lock the interface** (header lock icon) to prevent all interaction — useful when placing an iPad in a public-facing position.

---

## 7. Maintenance Tools

KeyLabRemote includes a set of maintenance tools for managing your fleet of KeyLab player machines. These are operator-level troubleshooting commands intended for situations where you need to restart a player, change its network settings, or perform other system-level operations remotely.

**Important**: Maintenance commands affect the system state of player machines. Some commands can make a player temporarily or permanently unreachable if used incorrectly. Always read the warnings below before using a command for the first time.

### Accessing Maintenance Tools

Maintenance tools are available from KeyLabRemote:

1. **Maintenance menu** -- A dedicated menu in the menu bar (after "Overlays"). All eight commands are available here. Select your target players first, then choose the command.
2. **Network Window** -- Toolbar menus at the bottom of the window with Player, Network, and Content menus for all maintenance actions. Select rows in the table first, then use the menu.

All maintenance commands require you to **select one or more players before the command becomes available**. If no players are selected, the menu items and toolbar buttons are grayed out.

### Batch Operations

All maintenance commands support batch execution. To target multiple players at once:

- **Cmd+Click** players in the player list or Network Players table to toggle individual selections
- **Shift+Click** to select a range of players
- **Cmd+A** to select all players in the Network Players window
- Use the **All** button in the Controller header to target every connected player

When you execute a command on multiple players, the confirmation dialog shows the count and names of all targeted players. The command is sent to all players simultaneously.

### Commands

#### Reboot Players

**Risk Level**: HIGH -- Double confirmation required

Sends a hardware reboot command to the selected players. The Mac will shut down and restart.

- **Keyboard shortcut**: `Ctrl+Opt+R`
- **Available from**: Maintenance menu (not in context menus or toolbar)
- The Player first tries `System Events` restart, which requires no password prompt if KeyLab has been granted **Accessibility permission** (System Settings > Privacy & Security > Accessibility). If Accessibility is not granted, it falls back to an admin password prompt on the player machine.
- After rebooting, the player will take 30-60 seconds to come back online (depending on boot speed).
- If Keep-Alive is enabled, KeyLab will auto-launch after the Mac restarts.
- If Keep-Alive is NOT enabled, only the Mac restarts -- KeyLab must be launched manually.
- **Setup tip**: Grant Accessibility permission to KeyLab once on each player machine to enable password-free reboots across the fleet.
- **Use when**: A player is unresponsive to all other commands, or you need a clean system restart.

#### Force Quit Players

**Risk Level**: HIGH -- Double confirmation required

Terminates the KeyLab app on the selected players. The app performs a clean network shutdown (releasing port 7778 and Bonjour registration) before exiting.

- **Keyboard shortcut**: `Ctrl+Opt+Q`
- If Keep-Alive is enabled, KeyLab will automatically restart within a few seconds.
- If Keep-Alive is NOT enabled, the app stays closed. You will need to manually launch it or use Reboot.
- Any in-progress file downloads or operations will be interrupted.
- **Use when**: The KeyLab app is frozen or misbehaving, and you need to kill and restart it quickly.

> **Warning**: If Keep-Alive is disabled on a player, force-quitting it will leave the player without KeyLab running. Make sure Keep-Alive is enabled before using this command, or be prepared to manually relaunch the app.

#### Relaunch Players

**Risk Level**: MEDIUM -- Single confirmation required

Gracefully restarts the KeyLab app. The current instance stops its network services (releasing port 7778 and Bonjour registration), launches a fresh copy of itself, then exits. The new instance starts clean.

- No keyboard shortcut (available from menu and toolbar only).
- Works regardless of Keep-Alive status -- the app self-relaunches before quitting.
- The previously loaded Keynote file will NOT be automatically reopened. You will need to re-send it.
- On Intel Macs, port release may take slightly longer. The new instance retries its network listener automatically (up to 5 attempts with progressive delays) until the port becomes available.
- **Use when**: You want to restart KeyLab cleanly without rebooting the entire Mac. Good for clearing minor glitches.

#### Toggle Keep-Alive

**Risk Level**: MEDIUM -- Single confirmation required

Enables or disables the macOS LaunchAgent that auto-starts KeyLab on login and auto-restarts it if it crashes or is force-quit.

- **Available from**: Maintenance menu (not in context menus or toolbar)
- When enabled: KeyLab starts automatically when the Mac boots and restarts if it exits for any reason.
- When disabled: KeyLab must be launched manually and will not auto-restart.
- Keep-Alive should be enabled on all production player machines.
- **Use when**: Setting up a new player (enable) or temporarily disabling auto-restart for debugging (disable).

#### Deploy Update

**Risk Level**: HIGH -- Double confirmation required

Pushes a new build of the KeyLab app to the selected players over the network. See the "OTA Player Updates" section in Best Practices for detailed instructions.

- **Available from**: Maintenance menu (not in context menus or toolbar)

#### Deploy Player / Deploy Server

**Risk Level**: MEDIUM -- Single confirmation required

Deploys a bundled `.pkg` to selected SSH-capable machines. The package is copied via SCP, installed with `sudo installer`, and cleaned up. For Player deployments, the LaunchAgent is restarted so the new version launches immediately.

- **Available from**: Maintenance > Player Maintenance menu, Network Window SSH toolbar menu, Network Window context menu
- **Only visible** when the corresponding `.pkg` is embedded in the Remote app bundle
- **Uses**: Fleet SSH credentials (same as Install Package)
- See "App Deployment" in Best Practices for build script details.

#### Set Network Config

**Risk Level**: HIGH -- Double confirmation required

Changes the network configuration on a player to either a static IP address or DHCP (automatic).

When you select this command, a configuration dialog appears where you choose:
- **DHCP**: The player will obtain its IP address automatically from the network router.
- **Static**: You must enter an IP address, subnet mask, and gateway (router) address.

**Batch IP range assignment** (multiple players, Static mode): When targeting more than one player in Static mode, the dialog shows an extended layout with:
- An **Increment** popup (+1, +2, +5, or +10) to control the spacing between IP addresses
- A **preview table** showing each player's name and its assigned IP address
- Players are sorted alphabetically. The base IP's last octet is incremented per player.
- If any IP would overflow past 255, the Apply button is disabled and a red error label appears.
- The second confirmation dialog shows the full IP range (e.g., "from 192.168.1.100 to 192.168.1.104").

> **Warning -- Read carefully**: Setting an incorrect static IP, subnet mask, or gateway can make the player completely unreachable from the Controller. There is NO automatic rollback. If you assign a wrong IP, you will need to physically access the player machine and fix the network settings in System Settings > Network.

Safety guidelines:
- Double-check the IP address, subnet mask, and gateway before confirming.
- For batch operations, review the preview table carefully before applying.
- Make sure the static IPs are on the same subnet as the Controller.
- Make sure the static IPs are not already in use by other devices on the network.
- When in doubt, use DHCP -- it is always safe.
- **Use when**: You need players on fixed IPs for a specific network configuration (e.g., a production show network with assigned addresses). Use DHCP when you want the network to assign addresses automatically.

#### Rename Hostname

**Risk Level**: HIGH -- Double confirmation required

Changes the computer name (hostname) of a player machine. This updates the ComputerName, LocalHostName, and HostName settings on the Mac.

- The rename requires an admin password prompt on the player machine (a single prompt covers all three hostname changes).
- After renaming, the player immediately republishes its Bonjour service with the new name, causing a brief disconnect.
- The player will reappear in the Controller within a few seconds under the new name.
- The old name will disappear from the player list.
- **Identity preservation**: Each player broadcasts a persistent hardware identifier (`machineID` / IOPlatformUUID). After renaming, the server and controller use this machineID to recognize the same physical machine under its new name. Room assignments, grid positions, aliases, and group memberships are automatically migrated to the new hostname.
- **Use when**: You want to give a player a meaningful name that reflects its physical location (e.g., "Stage-Left-1", "Lobby-Screen", "Balcony-Center").

> **Note**: The server automatically migrates the device record from the old hostname to the new hostname using the player's hardware machineID. There may be a brief transition period (a few seconds) while the Bonjour service republishes and the server correlates the identity.

#### Cycle Wi-Fi

**Risk Level**: HIGH -- Double confirmation required

Turns the Wi-Fi adapter off, waits 3 seconds, then turns it back on. This is equivalent to toggling Wi-Fi in System Settings.

- **Keyboard shortcut**: `Ctrl+Opt+W`
- The player will go offline for approximately 3-5 seconds during the cycle.
- After Wi-Fi comes back on, the player will reconnect to its saved Wi-Fi network and reappear in the Controller.
- If the player is connected via Ethernet (wired), cycling Wi-Fi has no effect on the primary connection.
- **Use when**: A player's Wi-Fi connection is degraded (slow or intermittent) and you want to force it to reconnect. This can resolve issues with Wi-Fi channel congestion or AP handoff failures.

> **Warning**: If the Wi-Fi network uses a captive portal (login page), the player may not automatically reconnect after cycling. You would need physical access to rejoin the network.

### Keyboard Shortcuts

| Shortcut | Command |
|----------|---------|
| `Ctrl+Opt+R` | Reboot Players |
| `Ctrl+Opt+Q` | Force Quit Players |
| `Ctrl+Opt+W` | Cycle Wi-Fi |
| `Option+Cmd+S` | Screen Share |

### Confirmation Dialogs

All maintenance commands require confirmation before execution:

- **MEDIUM risk** commands (Relaunch, Toggle Keep-Alive) show a single confirmation dialog with the action description and target player names.
- **HIGH risk** commands (Reboot, Force Quit, Deploy Update, Set Network Config, Rename Hostname, Cycle Wi-Fi) show two confirmation dialogs in sequence. The first describes the action; the second asks "Are you sure?" and is styled as a critical alert.

For batch operations, the confirmation dialog lists all targeted player names and the total count.

---

## 8. Troubleshooting

### Quick Fixes (Operator-Level)

| Problem | Fix |
|---------|-----|
| Players don't appear in the Controller | Check all devices are on the same Wi-Fi/Ethernet subnet. Restart the Player app. Approve the macOS "Local Network" permission prompt if it reappears (System Settings > Privacy & Security > Local Network). |
| Player shows red (offline) | Check network cable/Wi-Fi connection. Restart the Player app. Use "Remove Offline" in the Controller to clear stale entries. |
| Commands are delayed (>1 second) | Check for network congestion. Switch Players to Ethernet instead of Wi-Fi. Close bandwidth-heavy apps on the network. |
| Slides look wrong or missing elements | Re-send the `.key` file to the Player. Ensure the Keynote file was saved in the latest format. |
| Grading reset unexpectedly | Another Controller may have sent a Reset command. Coordinate with co-operators in multi-operator setups. |
| Blackout won't turn off | Click the Blackout button again (it's a toggle). Check if the controls section or interface is locked. |
| App seems frozen / unresponsive | Check if the interface is locked (lock icon in the header). Tap the lock overlay to unlock. |
| Server shows "Disconnected" | Restart KeyLabServer. The Controller caches state and will reconnect automatically within a few seconds. |
| Stale/offline players clutter the server roster | Open the Network Window (macOS). Offline devices appear with a gray dot and "Offline" label. Select them and click "Unassign from Server" to remove them from the roster. |
| Slides advance on wrong screens | Check which players are targeted. Use "Clear" to deselect all, then re-select only the intended targets. |
| Video playback out of sync | Press `K` to pause, then `K` again to restart playback. Or press `←` on slide 1 to reset and pause all videos. |
| Reboot prompts for admin password on player | Grant Accessibility permission to KeyLab (System Settings > Privacy & Security > Accessibility) on the player machine. This enables password-free reboots via System Events. |
| Maintenance command shows "permission denied" | The player Mac may have restricted admin access (MDM-managed). The system commands (networksetup, scutil) require admin privileges. Check the player's user account permissions. |
| Player unreachable after Set Network Config | The static IP may be incorrect or on a different subnet. Physically access the player Mac and open System Settings > Network to fix the IP configuration or switch back to DHCP. |
| Player disappeared after Rename Hostname | The player is re-advertising on Bonjour with its new name. Wait 5-10 seconds for it to reappear under the new name. The server and controller automatically recognize the same physical machine via its hardware machineID — room assignments, grid positions, and group memberships are preserved. If the player does not reappear, check the Network Players window. |
| Player offline after Cycle Wi-Fi | The player's Wi-Fi is reconnecting. Wait 5-10 seconds. If it does not come back, the Wi-Fi network may require a captive portal login or the player may have connected to a different network. Physically access the player to check. |
| Force Quit did not restart the app | Keep-Alive is not enabled on that player. Enable Keep-Alive first (Maintenance > Toggle Keep-Alive), then Force Quit. Or use Relaunch instead, which self-restarts regardless of Keep-Alive status. |
| "Update Already in Progress" but update finished | This is resolved in current builds. The Controller now detects player disconnection and reconnection during updates and automatically marks the update as complete. If you still see this on older builds, wait up to 120 seconds for automatic timeout recovery. |
| SSH "Install Package" or "Launch Player" fails with permission denied | Open Settings > Fleet Credentials and verify the SSH username and password are correct. The credentials must match an account on the target machine with SSH access enabled (System Settings > General > Sharing > Remote Login). |
| SSH operation prompts for credentials every time | The "Save as default" checkbox in the credential prompt must be checked, or configure credentials in Settings > Fleet Credentials before running the operation. |
| Deploy Player/Server buttons not visible | The `.pkg` files are not embedded in this build of KeyLabRemote. Run `pkg_player.sh`, `pkg_server.sh`, and `embed_packages.sh` to create a distribution build. See "App Deployment" in Best Practices. |
| Deploy fails with "Package Not Found" | The `.pkg` was not found in the app bundle's Resources directory. Re-run `embed_packages.sh` against the built `.app`. |
| "Check for Updates" is grayed out | Sparkle hasn't finished initializing. Wait a moment after launch. If it persists, check that `SUPublicEDKey` in Info.plist has been replaced with the actual EdDSA public key. |
| Extra screen not showing | Verify multi-screen mode is enabled and the display is connected. Check System Settings > Displays. |
| Screen grading out of sync | Each screen has independent grading. Adjust each screen separately from the Controller using screen targeting. |
| Screen stuck in fullscreen | Secondary windows are always fullscreen. If one appears frozen, try disabling and re-enabling multi-screen mode from the Controller. |

### Advanced Diagnostics (Technical Staff)

#### Bonjour Verification

Confirm services are being published on the network:

```bash
# Check for Players
dns-sd -B _keylablite._tcp.

# Check for Server
dns-sd -B _keylabserver._tcp.
```

Both commands should show registered services within a few seconds. If nothing appears, the app is not publishing — check network permissions and firewall settings.

#### Port Check

KeyLab uses two TCP ports:

| Service | Port | Bonjour Type |
|---------|------|--------------|
| KeyLab Player | 7778 | `_keylablite._tcp.` |
| KeyLabServer | 7780 | `_keylabserver._tcp.` |

Verify a port is listening:

```bash
lsof -i :7778    # Player
lsof -i :7780    # Server
```

#### macOS Firewall

System Settings > Network > Firewall — ensure **KeyLab** and **KeyLabServer** are listed as "Allow incoming connections." If the apps aren't listed, temporarily disable the firewall for testing, or add them manually.

#### Console Logs

Open Console.app and filter by subsystem to see real-time logs from all KeyLab apps:

```
subsystem == "creative.cerisano.bullpen"
```

Or use Terminal for a live log stream:

```bash
log stream --predicate 'subsystem == "creative.cerisano.bullpen"'
```

#### Network Interface Check

Verify your Mac has a valid local IP address:

```bash
ifconfig en0    # Ethernet
ifconfig en1    # Wi-Fi (may vary)
```

The IP should be an RFC 1918 private address: `10.x.x.x`, `192.168.x.x`, or `172.16.x.x`–`172.31.x.x`. KeyLab operates on LAN only and rejects non-local connections.

#### Reconnection Behavior

- **Player** auto-reconnects with a **1-second delay** after disconnect.
- **Server** reconnection uses a **2-second delay**.
- Health status transitions: green (healthy) → yellow after **4 seconds** of missed heartbeats → red after **10 seconds**.
- **Persistent identity**: Each player broadcasts a hardware `machineID` (IOPlatformUUID) in its state updates. When a player reconnects after a reboot, relaunch, or hostname rename, the controller and server use this machineID to recognize the same physical machine. Group memberships (controller-side) and device records including room assignments, grid positions, and aliases (server-side) are automatically migrated to the new connection/hostname.

#### App Nap

KeyLab Player and KeyLabServer disable App Nap automatically to ensure instant network responsiveness. If sluggish behavior occurs, verify in Activity Monitor (add the "App Nap" column) that the app shows "No."

#### Pack Transfer Failures

If a Keynote file transfer fails:

1. Check available disk space on the Player: `df -h`
2. Verify the file is not corrupted — re-export from Keynote
3. Re-send the file from the Controller
4. Check the Controller's log window for transfer error details

#### OTA Update Failures

If a player update fails:

1. Check the Controller's log window for the specific error (`UPDATE ERROR — {message}`).
2. **Checksum mismatch** — the download was corrupted. Retry.
3. **No .app bundle in archive** — the selected file was not a valid macOS `.app` bundle.
4. **Install failed** — the Player may lack write access to its `.app` parent directory. Check permissions.
5. If the new build crashes on launch, launchd will keep restarting it (with a 10-second throttle). To roll back, SSH into the Player and rename `KeyLab.app.bak` back to `KeyLab.app`.
6. Ensure the Player has enough disk space (~3x the app size: download + extraction + backup).
7. **"Update Already in Progress"** — in current builds, the Controller detects player disconnection/reconnection during updates and automatically finalizes the update. A 120-second safety timeout also acts as a backstop. If the issue persists on older builds, restart KeyLabRemote.

---

## 9. Glossary

| Term | Definition |
|------|------------|
| **Player** | A Mac running KeyLab that displays slides on a screen. |
| **PlayerScreenState** | The per-screen state object on the Player side. Each screen has its own `PlayerScreenState` instance holding slide index, grading values, overlay state, and canvas callbacks. |
| **Controller / Remote** | A Mac or iPad running KeyLabRemote that sends commands to Players. |
| **Server** | A Mac running KeyLabServer for centralized state management (optional). |
| **Classic Mode** | Direct controller-to-player operation without a server. |
| **Extended Mode** | Server-backed operation with rooms, grid layouts, and multi-operator support. |
| **Grading** | Brightness, contrast, color temperature, and RGB channel adjustments applied to a Player's display output. |
| **Color Temperature** | Measured in Kelvin (K). Lower values (2000K–4500K) produce warm amber/orange tones; higher values (7500K–9500K) produce cool blue tones. 6500K is neutral daylight (D65). Internally applied using Apple's CITemperatureAndTint filter with mired-space mirroring. |
| **Mired** | Micro reciprocal degree (1,000,000 / Kelvin). A perceptually uniform scale for color temperature — equal mired shifts produce equal visual shifts, unlike the Kelvin scale which is nonlinear. |
| **VFX** | Visual effects overlay — colored marker shapes (plus, circle, square) rendered on top of slides at corner and center positions. |
| **Pack** | A bundled set of rendered slides sent from Controller to Player for display. |
| **Build** | An animation step within a single Keynote slide. A slide may have multiple builds that play in sequence. |
| **Blackout** | Full black overlay that hides the presentation. Can be toggled instantly or with a configurable fade duration. |
| **Whiteout** | Full white overlay that hides the presentation. |
| **Grid** | 2D room layout view in Extended Mode showing the physical positions of display screens, walls, doors, and other room elements. |
| **Room** | A named space in Extended Mode containing a set of Players and a grid layout. Managed by the Server. |
| **Bonjour** | Apple's zero-configuration networking protocol (mDNS/DNS-SD) used by KeyLab for automatic device discovery on the LAN. |
| **Target** | A Player selected to receive commands from the Controller. Multiple Players can be targeted simultaneously. |
| **Screen Snapshot** | A lightweight snapshot of a single screen's state (slide, grading, overlays) broadcast as part of the player's `LitePlayerState` for remote monitoring. |
| **Snapshot** | An exported capture of the current Controller state (player assignments, grading, etc.) that can be restored later. |
| **OTA Update** | Over-the-air player update. The Controller pushes a new KeyLab.app build to Players over the LAN — download, verify, replace, and restart — without requiring SSH or physical access. |
| **Keep-Alive** | A macOS LaunchAgent that auto-starts the Player on login and auto-restarts it on crash or exit. Required for OTA updates so that launchd relaunches the new binary after the swap. |
| **Machine ID** | A persistent hardware identifier (IOPlatformUUID) that uniquely identifies a physical Mac. Unlike the hostname, machineID survives reboots, relaunches, OS reinstalls, and hostname renames. Used by the server and controller to recognize returning players and preserve their assignments. |
| **Maintenance Commands** | A set of operator-level troubleshooting tools in KeyLabRemote for managing player machines remotely: reboot, force quit, relaunch, network configuration, hostname rename, Wi-Fi cycling. |
| **Force Quit** | Immediately terminates the KeyLab app on a player without graceful shutdown. If Keep-Alive is enabled, the app auto-restarts. |
| **Relaunch** | Gracefully restarts the KeyLab app by spawning a new instance before quitting the current one. Works regardless of Keep-Alive status. |
| **Cycle Wi-Fi** | Turns the Wi-Fi adapter off and back on after a short delay, forcing the player to reconnect to the wireless network. |
| **Display Index** | A zero-based integer identifying which screen on a player receives a command. 0 = primary (main display), 1 = second screen, 2 = third screen, etc. Supports up to 8 screens. |
| **Double Confirmation** | A safety pattern used for HIGH risk maintenance commands. Two sequential confirmation dialogs must be approved before the command executes. |
| **Multi-Screen Mode** | A player feature that enables independent rendering on all connected displays (up to 8). Each screen has its own slide position, grading, and overlays. Enabled by default when additional displays are detected. |
| **Fleet Credentials** | Shared SSH username/password and Wi-Fi SSID/password stored in Settings for use across all managed machines. SSH passwords and Wi-Fi passwords are stored in the macOS Keychain. |
| **Install Package** | An SSH-based operation that copies a `.pkg` file to remote machines via SCP and installs it using `sudo installer`. Runs in parallel across all selected machines and shows a single summary alert with per-machine results. Requires fleet SSH credentials. |
| **Launch Player** | An SSH-based operation that remotely opens `KeyLab.app` on a machine via SSH `open -a KeyLab`. Requires fleet SSH credentials. |
| **SSH_ASKPASS** | A mechanism used by KeyLab Remote for non-interactive SSH password authentication. A temporary script provides the password to SSH without requiring a terminal prompt. |
| **Deploy Player** | An SSH-based operation that copies the bundled KeyLab Player `.pkg` to remote machines via SCP, installs it with `sudo installer`, and restarts the Player LaunchAgent. Only available when the `.pkg` is embedded in the Remote app bundle. |
| **Deploy Server** | An SSH-based operation that copies the bundled KeyLab Server `.pkg` to remote machines via SCP and installs it with `sudo installer`. Only available when the `.pkg` is embedded in the Remote app bundle. |
| **App Catalog** | A registry of deployable `.pkg` packages bundled inside KeyLabRemote's Resources directory. Provides version information from companion `.version.plist` sidecar files written by the build scripts. |
| **Sparkle** | An open-source macOS framework used by KeyLabRemote for automatic self-updates. Checks an appcast XML feed for new versions, verifies EdDSA signatures, and handles download and installation. |
| **Appcast** | An XML file (similar to RSS) that describes available software updates for Sparkle. Contains version numbers, download URLs, release notes, and cryptographic signatures. Hosted as a GitHub Release asset. |

---

*KeyLab — Multi-Screen Keynote Control System*
