# Fenestra Threat Model

## Scope

This model covers Fenestra v1 (milestone M1): View-only streaming between a Mac and an iPhone or iPad on the same home Wi-Fi network. Threats that only apply to later milestones are marked with the milestone they apply to. This document must be revisited before M3 adds remote control and before remote access outside the home network is added.

## System Overview

Fenestra v1 has two apps that talk directly to each other over the local network. There is no server in between.

### Components

- **Mac app (host):** Captures the screen and streams it to a paired device.
- **iPhone/iPad app (viewer):** Finds Macs, pairs with them and displays the stream.
- **Keychain (on each device):** Stores pairing credentials (NFR-6).
- **macOS Screen Recording permission:** The system setting that allows the Mac app to capture the screen (FR-1).
- **Local network:** The home Wi-Fi that both devices share. Fenestra does not control it.

### Data Flows

1. **Discovery:** The Mac app advertises itself on the local network and the viewer lists the Macs it finds by name (FR-2, FR-3).
2. **Pairing (first connection only):** The viewer asks to connect, the user approves it on the Mac and both devices store a credential for each other (FR-4).
3. **Connection:** On later connections, both devices prove who they are with the stored credentials before anything else is sent.
4. **Streaming:** The Mac sends video frames to the viewer (FR-5, FR-6).
5. **Control messages:** The viewer sends small messages back, like disconnect and reconnect (FR-7, FR-8). In M3 these will also user input

### Trust Boundaries

- **Device ↔ network:** Anything arriving over Wi-Fi may come from an attacker until it has been authenticated.
- **Mac app ↔ macOS:** The app can only capture the screen if the user grants screen recording permission.

## Assets

| Asset | Why it matters | Milestone |
|---|---|---|
| Screen contents | Anything on screen can be exposed: passwords, banking, messages, notifications. | M1 |
| Pairing credentials | Whoever holds them can connect as a trusted device. | M1 |
| Paired device list | Adding a device to it grants that device access. | M1 |
| The Mac itself | The Mac app accepts network connections, so a bug in how it handles them could let an attacker crash it or run code. | M1 |
| The iPhone/iPad itself | The viewer accepts video from the network, so a malicious host could send malformed data to crash the app or run code. | M1 |
| Stream availability | The user should be able to stream when they want to; disruption is an annoyance rather than a leak. | M1 |
| Window list | Window titles can reveal private information (email subjects, document names, sites) even without any video. | M2 |
| Share selection | When the user shares one window, nothing outside that window should be visible. | M2 |
| Control of the Mac | Mouse and keyboard input from the phone controls the Mac fully. | M3 |

## Threats

| ID | Threat | STRIDE | Asset | Mitigation | Requirement |
|---|---|---|---|---|---|
| TH-1 | An unknown phone connects to the Mac pretending to be a trusted device. | S | Screen contents | New devices can only request pairing while pairing mode is turned on in the Mac app. The first connection requires approval on the Mac plus a pairing code shown on the Mac and entered on the phone. Later connections must prove identity with the stored credential. | FR-4, FR-9, FR-10, NFR-6 |
| TH-2 | A fake Mac on the network advertises itself as the user's Mac. | S | The iPhone/iPad itself | The viewer only connects without re-pairing to a Mac that proves it holds the credential from pairing. | FR-4 |
| TH-3 | A lost or stolen phone that is already paired is used to connect. | S | Screen contents | The pairing credential can only be used after Face ID or the device passcode, checked once each time the app is opened. The Mac app also lets the user see and remove paired devices. | FR-12, FR-13 |
| TH-4 | An attacker between the devices alters or injects frames, so the user sees a fake screen. Becomes more serious in M3, when the user acts on what they see. | T | Screen contents | The encrypted connection is also authenticated, so any altered data is detected and rejected. | NFR-5 |
| TH-5 | An attacker records valid messages and resends them later (replay). | T | Paired device list | Every message is unique and ordered, and the protocol rejects repeats. | NFR-5 |
| TH-6 | Other software on the Mac edits the paired device list to add an attacker's device. | T | Paired device list | The paired device list and its credentials are stored in the Keychain, not in plain files. | NFR-6 |
| TH-7 | A device watches the screen and there is no record of it. | R | Screen contents | The Mac app keeps a local log of pairings, connections, disconnections, and removed devices. Devices are identified by their pairing credential, not only their name. The log never contains screen contents. | FR-15, NFR-9 |
| TH-8 | Streaming happens without the user noticing. | R | Screen contents | While streaming, the Mac shows a menu bar indicator naming the connected device, plus a notification when a device connects. | FR-7, FR-14 |
| TH-9 | Someone on the network records the stream. | I | Screen contents | All traffic is encrypted, with no unencrypted fallback. | NFR-5 |
| TH-10 | The discovery broadcast reveals the Mac's name, which often includes the owner's name, to everyone on the network. | I | Screen contents | Advertise only what is needed to connect, let the user rename the Mac in the app, and only advertise while sharing is turned on. | FR-2, FR-11 |
| TH-11 | The last streamed frame is saved on the phone by the iOS app switcher snapshot. | I | Screen contents | Hide the stream when the app leaves the foreground, and never write frames to disk or logs. | NFR-9 |
| TH-12 | Pairing credentials are copied off the phone or Mac. | I | Pairing credentials | Credentials are stored in the Keychain, never in plain files. | NFR-6 |
| TH-13 | (M2) Window titles are read off the network. | I | Window list | The window list is only sent to an authenticated device over the encrypted connection. | NFR-5 |
| TH-14 | An attacker floods the Mac with connection requests so legitimate devices can't connect. | D | Stream availability | Limit how fast connection attempts are accepted, drop handshakes that don't finish quickly, and do no expensive work until a device is authenticated. | NFR-10 |
| TH-15 | An attacker sends repeated pairing requests so the Mac keeps showing approval prompts, hoping the user eventually clicks Allow. | D | Paired device list | Pairing requests are ignored unless pairing mode is on, and only one prompt is shown at a time. | FR-4, FR-9 |
| TH-16 | A single malformed message crashes the Mac app or the viewer. | D | The Mac itself, The iPhone/iPad itself | Every incoming message is validated before use; invalid data ends the connection instead of crashing the app. Message parsing is fuzz tested. | *(architecture)* |
| TH-17 | A paired device uses a modified client to send messages a viewer isn't allowed to send, like turning on pairing mode or adding devices. | E | Paired device list | The Mac checks every message against what that device is allowed to do and never relies on restrictions in the viewer app. | NFR-11 |
| TH-18 | (M2) The viewer asks for a window or the full desktop when the user chose to share only one window. | E | Share selection | The Mac decides what may be shared; the viewer can only choose from what the Mac allows. | *(M2)* |
| TH-19 | A bug in message handling lets an attacker run code inside the Mac app. | E | The Mac itself | Input is validated and fuzz tested (see TH-16), and the app runs in the macOS App Sandbox with only the permissions it needs. | *(architecture)* |
| TH-20 | (M3) A device that can watch is used to control the Mac. | E | Control of the Mac | Control is a separate permission granted per device, and the Mac only requests the Accessibility permission once control is turned on. | *(M3)* |

## Accepted Risks

| Risk | Why it's accepted |
|---|---|
| Malware already running with full control of the Mac or phone. | It can already see the screen without Fenestra. Protecting a compromised device is the operating system's job. |
| Someone jams or disrupts the Wi-Fi. | No app can prevent this. Fenestra shows the disconnect and reconnects when the network returns (FR-8). |
| Someone looks at the phone's screen, or takes a screenshot or screen recording on it. | Once frames are shown to the user, Fenestra can't control what happens to them. |
| Someone who knows the phone's passcode uses a stolen phone before the user removes it from the Mac. | The passcode fallback is needed for devices or moments where Face ID fails. The risk ends once the user removes the device (TH-3). |
| Discovery reveals that a Fenestra Mac is on the network. | Discovery is required for FR-2. TH-10 limits what it reveals. |

## Review

This model is reviewed at the end of each milestone and whenever the design changes.

At each review:

- Confirm every mitigation for that milestone is built, and tested where possible.
- Add any new threats found during development, using the next unused ID. Existing IDs are never renumbered; a threat that no longer applies is marked as withdrawn instead of deleted.
- Update Accepted Risks if anything has changed.

| Date | Milestone | Notes |
|---|---|---|
| 2026-10-28 | M0 | Initial threat model. |