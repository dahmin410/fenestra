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
| TH-1 | An unknown phone connects to the Mac pretending to be a trusted device. | S | Screen contents | First connection requires approval on the Mac plus a pairing code shown on the Mac and entered on the phone. Later connections must prove identity with the stored credential. | FR-4, NFR-6 |
| TH-2 | A fake Mac on the network advertises itself as the user's Mac. | S | The iPhone/iPad itself | The viewer only connects without re-pairing to a Mac that proves it holds the credential from pairing. | FR-4 |
| TH-3 | A lost or stolen phone that is already paired is used to connect. | S | Screen contents | The Mac app lets the user see and remove paired devices. | *(new)* |

## Accepted Risks

## Review