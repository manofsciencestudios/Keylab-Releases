# KeyLab OSC Glossary — 2026-02-12

---

## Table of Contents

1. [Overview](#1-overview)
2. [OSC Basics](#2-osc-basics)
3. [Connection Setup](#3-connection-setup)
4. [Standard Commands (Selection-Based)](#4-standard-commands-selection-based)
5. [Per-Player Targeting](#5-per-player-targeting)
6. [Per-Group Targeting](#6-per-group-targeting)
7. [All-Player Targeting](#7-all-player-targeting)
8. [Selection Commands](#8-selection-commands)
9. [Query Commands](#9-query-commands)
10. [Feedback Messages (Output)](#10-feedback-messages-output)
11. [Name Conventions](#11-name-conventions)
12. [Integration Examples](#12-integration-examples)
13. [Terminology](#13-terminology)

---

## 1. Overview

KeyLabRemote accepts and sends OSC (Open Sound Control) messages over UDP, enabling external show-control tools to drive Bullpen presentations. OSC support covers three targeting modes:

- **Selection-based** — Commands follow the current UI selection (same as clicking in KeyLabRemote)
- **Direct targeting** — Commands fire at a named player, named group, or all players without changing the UI
- **Selection commands** — OSC messages that change which players are selected in the UI

All OSC messages use the `/bullpen/` address prefix.

---

## 2. OSC Basics

| Property | Value |
|----------|-------|
| **Protocol** | UDP |
| **Default port** | `8000` (configurable in Settings > OSC) |
| **Spec** | OSC 1.0 |
| **Argument types** | `int32` (i), `float32` (f), `string` (s), `blob` (b), `true` (T), `false` (F), `nil` (N), `impulse` (I) |

An OSC message has two parts:
- **Address** — a hierarchical path like `/bullpen/slide/next`
- **Arguments** — zero or more typed values (e.g., an integer slide number)

---

## 3. Connection Setup

1. Open **KeyLabRemote** > **Settings** > **OSC**
2. Check **Enable OSC**
3. Set the **Receive Port** (default `8000`)
4. (Optional) Enable **Feedback** and add destination `host:port` pairs for bidirectional OSC

External tools (Stream Deck, QLab, TouchOSC, lighting consoles) send UDP messages to the Controller's IP address on the configured port.

---

## 4. Standard Commands (Selection-Based)

These commands target whatever is currently selected in the KeyLabRemote UI — individual players, groups, or all players. This is the same behavior as pressing buttons in the app.

### Navigation

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/slide/next` | — | Advance to next slide or build |
| `/bullpen/slide/prev` | — | Go to previous slide or build |
| `/bullpen/slide/goto` | `int` index | Jump to slide number (1-indexed from OSC, 0-indexed internally) |
| `/bullpen/build/next` | — | Next build (same as next slide — builds are handled internally) |
| `/bullpen/build/prev` | — | Previous build |
| `/bullpen/shuffle` | — | Randomly assign slides across targeted players |

### Blackout & Display

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/blackout` | — | Toggle blackout on/off |
| `/bullpen/blackout/on` | — | Enable blackout |
| `/bullpen/blackout/off` | — | Disable blackout |
| `/bullpen/dim` | `int` 0–100 *or* `float` 0–100 | Set dim overlay percentage |

### Video Control

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/video/play` | — | Resume video playback |
| `/bullpen/video/pause` | — | Pause video playback |
| `/bullpen/video/toggle` | — | Toggle play/pause |
| `/bullpen/video/speedup` | — | Increase video playback speed |
| `/bullpen/video/slowdown` | — | Decrease video playback speed |

### Color Temperature

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/temp` | `int` -100 to 100 *or* `float` | Adjust warm/cool bias relative to current Kelvin |
| `/bullpen/temp/reset` | — | Reset to neutral daylight (6500K) |
| `/bullpen/kelvin` | `int` 1800–12000 | Set absolute color temperature in Kelvin |

### Contrast

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/contrast` | `float` 0.0–2.0 | Set contrast multiplier (1.0 = neutral) |
| `/bullpen/contrast/adjust` | `float` delta | Adjust contrast relative to current value |

### RGB Levels

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/rgb` | `float` R, `float` G, `float` B | Set per-channel gain levels (0.0–1.0 each) |

### Brightness

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/brightness/adjust` | `int` delta | Adjust brightness relative to current dim value |

### Name Overlay

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/names/show` | — | Show player name overlays on all connected players |
| `/bullpen/names/hide` | — | Hide player name overlays |
| `/bullpen/names/toggle` | — | Toggle name overlay visibility |

### VFX Overlay

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/vfx` | — | Toggle VFX on/off |
| `/bullpen/vfx/on` | — | Enable VFX overlay |
| `/bullpen/vfx/off` | — | Disable VFX overlay |
| `/bullpen/vfx/cycle` | `int` direction (optional) | Cycle through VFX presets (+1 forward, -1 backward; default +1) |

### Logo Overlay

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/logo` | — | Toggle logo overlay on/off |
| `/bullpen/logo/on` | — | Enable logo overlay |
| `/bullpen/logo/off` | — | Disable logo overlay |

### Player Message

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/message` | `string` text | Show message text on targeted players |
| `/bullpen/message/show` | — | Show the current message |
| `/bullpen/message/hide` | — | Hide the current message |
| `/bullpen/message/clear` | — | Clear message text and hide |

### Grading Reset

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/grading/reset` | — | Reset dim, Kelvin, and contrast to defaults (0%, 6500K, 1.0) |

---

## 5. Per-Player Targeting

Send a command to a **specific player by Bonjour name**, without changing the UI selection. The address format is:

```
/bullpen/player/<name>/<command>
```

The `<name>` segment is the player's Bonjour name with **spaces replaced by underscores** and **case-insensitive** matching. The `<command>` portion uses the same paths as the standard commands (without the `/bullpen` prefix).

### Examples

| Address | Effect |
|---------|--------|
| `/bullpen/player/Stage_Left/slide/next` | Next slide on "Stage Left" only |
| `/bullpen/player/Mac_Mini_01/blackout/on` | Blackout on "Mac Mini 01" |
| `/bullpen/player/Lobby/dim` 50 | Set 50% dim on "Lobby" |
| `/bullpen/player/stage_left/slide/goto` 3 | Go to slide 3 on "Stage Left" (case-insensitive) |
| `/bullpen/player/Front_Screen/vfx/on` | Enable VFX on "Front Screen" |
| `/bullpen/player/Center/kelvin` 4000 | Set 4000K warm tone on "Center" |
| `/bullpen/player/DJ_Booth/message` "STANDBY" | Show "STANDBY" message on "DJ Booth" |

### Full Command List

Every command from [Section 4](#4-standard-commands-selection-based) works as a player-targeted command. Replace `/bullpen/` with `/bullpen/player/<name>/`:

| Standard Address | Player-Targeted Address |
|-----------------|------------------------|
| `/bullpen/slide/next` | `/bullpen/player/<name>/slide/next` |
| `/bullpen/blackout/on` | `/bullpen/player/<name>/blackout/on` |
| `/bullpen/dim` | `/bullpen/player/<name>/dim` |
| `/bullpen/video/play` | `/bullpen/player/<name>/video/play` |
| `/bullpen/grading/reset` | `/bullpen/player/<name>/grading/reset` |
| *(etc. for all commands)* | |

> **Note:** Direct player targeting does **not** change the UI selection in KeyLabRemote. The command fires at the named player and returns — the operator's current selection remains untouched.

---

## 6. Per-Group Targeting

Send a command to **all players in a named group**, without changing the UI selection:

```
/bullpen/group/<name>/<command>
```

The `<name>` is the group name with spaces replaced by underscores, case-insensitive.

### Examples

| Address | Effect |
|---------|--------|
| `/bullpen/group/Front_Row/slide/next` | Next slide on all "Front Row" members |
| `/bullpen/group/Front_Row/blackout/on` | Blackout the entire "Front Row" group |
| `/bullpen/group/Lobby_Screens/dim` 75 | Set 75% dim on "Lobby Screens" group |
| `/bullpen/group/Stage/video/play` | Play video on all players in "Stage" group |
| `/bullpen/group/VIP/kelvin` 3500 | Warm color temp on "VIP" group |
| `/bullpen/group/ALL_Screens/grading/reset` | Reset grading on "ALL Screens" group |

> **Note:** If a group name is not found or the group is empty, the command is silently dropped with a warning in the log.

---

## 7. All-Player Targeting

Send a command to **every connected player**, without changing the UI selection:

```
/bullpen/all/<command>
```

### Examples

| Address | Effect |
|---------|--------|
| `/bullpen/all/slide/next` | Next slide on every player |
| `/bullpen/all/blackout/on` | Blackout all players |
| `/bullpen/all/slide/goto` 1 | Jump to slide 1 on all players |
| `/bullpen/all/grading/reset` | Reset grading on all players |
| `/bullpen/all/dim` 0 | Clear dim on all players |
| `/bullpen/all/names/show` | Show name overlays on all players |

---

## 8. Selection Commands

These commands **change the UI selection** in KeyLabRemote — the same as clicking a player tile or group in the sidebar. After a selection command, subsequent standard commands (Section 4) will target the new selection.

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/select/player` | `string` name | Select a single player by Bonjour name |
| `/bullpen/select/group` | `string` name | Select a group by group name |
| `/bullpen/select/all` | — | Enable target-all mode |
| `/bullpen/select/clear` | — | Clear all selection |

The `name` argument supports underscores in place of spaces (e.g., `Stage_Left` or `Stage Left` both work).

### Workflow Example

A lighting console might send this sequence to select a group and then advance its slides:

```
/bullpen/select/group  "Front Row"     → selects the "Front Row" group in the UI
/bullpen/slide/next                    → advances slides on "Front Row" (the current selection)
/bullpen/slide/next                    → advances again
/bullpen/select/player "Stage Left"    → switches selection to a single player
/bullpen/blackout/on                   → blacks out "Stage Left" only
```

> **Note:** Selection commands DO change the UI — the operator will see the selection change in KeyLabRemote. Use direct targeting (Sections 5–7) if you want to fire commands without touching the UI.

---

## 9. Query Commands

Query commands request information about the current state. Responses are sent as feedback messages (Section 10) to all configured feedback destinations.

| Address | Response Address | Response Content |
|---------|-----------------|------------------|
| `/bullpen/query/players` | `/bullpen/status/players` | Comma-separated list of connected player names |
| `/bullpen/query/groups` | `/bullpen/status/groups` | Comma-separated list of group names |
| `/bullpen/query/selection` | `/bullpen/status/selection` | Current selection state (see below) |

### Selection State Values

The `/bullpen/status/selection` response contains a single string argument:

| Value | Meaning |
|-------|---------|
| `all` | Target-all mode is active |
| `none` | Nothing is selected |
| `player:Stage Left` | A single player named "Stage Left" is selected |
| `group:Front Row` | A group named "Front Row" is selected |

---

## 10. Feedback Messages (Output)

When feedback is enabled (Settings > OSC > Feedback), KeyLabRemote broadcasts state changes to all configured feedback destinations.

### Automatic State Feedback

These are sent whenever the corresponding state changes:

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/status/blackout` | `int` (0 or 1) | Blackout state changed |
| `/bullpen/status/slide` | `int` index, `int` count | Current slide position changed |
| `/bullpen/status/connected` | `int` count | Number of connected players changed |

### Query Response Feedback

These are sent in response to query commands (Section 9):

| Address | Arguments | Description |
|---------|-----------|-------------|
| `/bullpen/status/players` | `string` names | Comma-separated list of connected player Bonjour names |
| `/bullpen/status/groups` | `string` names | Comma-separated list of group names |
| `/bullpen/status/selection` | `string` state | Current selection state (`all`, `none`, `player:<name>`, `group:<name>`) |

---

## 11. Name Conventions

### Address-to-Name Mapping

OSC addresses cannot contain spaces. KeyLab uses a simple mapping:

| Display Name | OSC Address Segment |
|-------------|-------------------|
| `Stage Left` | `Stage_Left` |
| `Mac Mini 01` | `Mac_Mini_01` |
| `Front Row Center` | `Front_Row_Center` |
| `player1` | `player1` |

**Rules:**
- Spaces in names become underscores (`_`) in OSC addresses
- Matching is **case-insensitive** — `stage_left`, `Stage_Left`, and `STAGE_LEFT` all resolve to "Stage Left"
- Hyphens, periods, and other characters pass through unchanged
- Bonjour names are typically the Mac's hostname (e.g., `Joes-Mac-Mini` or `Stage Left`)
- Group names are defined by the operator in KeyLabRemote

### Finding Player and Group Names

Use the query commands to discover names:

```bash
# List all connected players
oscsend <controller-ip> 8000 /bullpen/query/players

# List all groups
oscsend <controller-ip> 8000 /bullpen/query/groups
```

Or check the KeyLabRemote sidebar — player names appear next to each connection tile, and group names appear as expandable section headers.

---

## 12. Integration Examples

### Stream Deck (via Companion)

Configure Bitfocus Companion with a "Generic OSC" module pointed at the Controller's IP and port:

```
Button 1: /bullpen/slide/next                        → Next slide (selection)
Button 2: /bullpen/slide/prev                        → Previous slide (selection)
Button 3: /bullpen/blackout                          → Toggle blackout (selection)
Button 4: /bullpen/player/Stage_Left/slide/next      → Next slide on Stage Left only
Button 5: /bullpen/group/Front_Row/blackout/on       → Blackout Front Row group
Button 6: /bullpen/all/slide/goto  1                 → Reset all to slide 1
Button 7: /bullpen/select/all                        → Select all players
Button 8: /bullpen/select/player  "Stage Left"       → Select Stage Left in UI
```

### QLab

In QLab, create OSC cues with the Destination set to the Controller's IP:port:

```
Cue 1:  /bullpen/select/group    "Main Stage"     → Select group
Cue 2:  /bullpen/slide/goto      5                → Go to slide 5
Cue 3:  /bullpen/blackout/off                      → Reveal
Cue 10: /bullpen/all/grading/reset                 → Reset all grading
```

### TouchOSC

Build a custom control surface with:
- Player selection buttons sending `/bullpen/select/player <name>`
- Slide navigation buttons sending `/bullpen/slide/next` and `/bullpen/slide/prev`
- A dim fader sending `/bullpen/dim <int>`
- Direct-target buttons like `/bullpen/player/Stage_Left/blackout/on`
- Feedback listeners on `/bullpen/status/slide` for current slide display

### Command-Line Testing

```bash
# Install oscsend (macOS)
brew install liblo

# Standard commands (follow UI selection)
oscsend localhost 8000 /bullpen/slide/next
oscsend localhost 8000 /bullpen/slide/goto i 5
oscsend localhost 8000 /bullpen/blackout/on
oscsend localhost 8000 /bullpen/dim i 50

# Direct player targeting
oscsend localhost 8000 /bullpen/player/Stage_Left/slide/next
oscsend localhost 8000 /bullpen/player/Mac_Mini_01/blackout/on

# Direct group targeting
oscsend localhost 8000 /bullpen/group/Front_Row/slide/goto i 3

# All-player targeting
oscsend localhost 8000 /bullpen/all/slide/goto i 1
oscsend localhost 8000 /bullpen/all/grading/reset

# Selection commands
oscsend localhost 8000 /bullpen/select/player s "Stage Left"
oscsend localhost 8000 /bullpen/select/group s "Front Row"
oscsend localhost 8000 /bullpen/select/all
oscsend localhost 8000 /bullpen/select/clear

# Query commands
oscsend localhost 8000 /bullpen/query/players
oscsend localhost 8000 /bullpen/query/groups
oscsend localhost 8000 /bullpen/query/selection
```

---

## 13. Terminology

| Term | Definition |
|------|------------|
| **OSC** | Open Sound Control — a UDP-based messaging protocol widely used in live performance, AV, and show control applications. |
| **Address** | The hierarchical path of an OSC message (e.g., `/bullpen/slide/next`). Determines what action to perform. |
| **Argument** | A typed value attached to an OSC message (e.g., an `int` slide number or a `string` player name). |
| **Standard command** | An OSC command that targets the current UI selection (e.g., `/bullpen/slide/next`). |
| **Direct targeting** | An OSC command that targets a specific player, group, or all players by name, bypassing the UI selection (e.g., `/bullpen/player/Stage_Left/slide/next`). |
| **Selection command** | An OSC command that changes which players or groups are selected in the KeyLabRemote UI (e.g., `/bullpen/select/player`). |
| **Query command** | An OSC command that requests state information; the response is sent as a feedback message (e.g., `/bullpen/query/players`). |
| **Feedback** | OSC messages sent from KeyLabRemote back to external tools, reporting state changes like slide position, blackout, or connection count. |
| **Bonjour name** | The network name a Mac advertises via mDNS/DNS-SD. Used as the player identifier in OSC addresses. Typically the Mac's hostname. |
| **Group name** | The name assigned to a player group in KeyLabRemote. Used in group-targeted OSC commands. |
| **Sanitize** | Convert a display name to an OSC-safe address segment: spaces become underscores, result is lowercased. `Stage Left` becomes `stage_left`. |
| **Desanitize** | Convert an OSC address segment back to a display name: underscores become spaces. `stage_left` becomes `stage left`. |
| **Target-all mode** | A state where all connected players receive commands, regardless of individual selection. Activated by `/bullpen/select/all` or the "All" button in the UI. |
| **Feedback destination** | A `host:port` pair where KeyLabRemote sends outgoing OSC feedback messages. Configured in Settings > OSC. |
| **UDP** | User Datagram Protocol — a connectionless transport used by OSC for low-latency message delivery. |
| **Port** | The UDP port number KeyLabRemote listens on for incoming OSC messages. Default `8000`, configurable in Settings. |

---

*KeyLab OSC Glossary — Per-Player & Per-Group Targeting*
