# Fenestra Threat Model

## Scope

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

## Threats

## Accepted Risks

## Review