# Fenestra Requirements

## Overview

Fenestra is a free, open-source app that streams your Mac's screen or windowed applications to your iPhone or iPad over your home Wi-Fi. Instead of being tied to your desk while your Mac works, you can step away and check in from your phone, whether it's a long build, a Mac mini with no monitor, or a Mac-only app. Phone control and access outside of your home network are planned for future releases.

## Functional Requirements

### M1: View the desktop

- **FR-1:** The Mac app must request Screen Recording permission and explain why it's needed. If permission is denied, it must show how to enable it.
- **FR-2:** The Mac app must advertise itself on the local network so iPhones can find it.
- **FR-3:** The iPhone app must list available Macs on the same Wi-Fi network by name.
- **FR-4:** The first connection from a new device must be approved on the Mac. Later connections from the same device must not require approval again.
- **FR-5:** The Mac app must capture the main display and stream it to the connected device.
- **FR-6:** The iPhone app must show the stream live, fit to the screen without stretching, with pinch-to-zoom.
- **FR-7:** The Mac app must show when it is streaming and let the user stop streaming at any time.
- **FR-8:** If the connection drops, the iPhone app must show that it's disconnected and try to reconnect automatically.

## Non-Functional Requirements

### Performance

- **NFR-1:** End-to-end latency must be under 100 ms at p95 on a typical home Wi-Fi network, with under 50 ms as the goal.
- **NFR-2:** The stream must run at 30 fps or higher, should reach 60 fps, and should match up to 120 fps when both the Mac display and the device support it.
- **NFR-3:** Streaming should use less than 20% of the Mac's CPU, with under 10% as the goal.

### Reliability

- **NFR-4:** After Wi-Fi returns, the iPhone app must reconnect within 5 seconds without user action, with under 2 seconds as the goal.

### Security & Privacy

- **NFR-5:** All traffic between devices must be encrypted.
- **NFR-6:** Pairing credentials must be stored in the system Keychain, not in plain files.
- **NFR-7:** No screen data may leave the local network, and the app must not collect analytics.

### Compatibility

- **NFR-8:** Minimum supported macOS and iOS versions will be decided in an ADR.

## Milestones

### M0: Foundation

Project skeleton with no streaming yet.

- Xcode project with Mac and iPhone apps that build and launch
- GitHub Actions builds and tests every pull request
- Requirements, threat model, and architecture docs written

### M1: View the desktop

- FR-1 through FR-8 complete
- NFR-1 through NFR-7 measured, with results recorded against their musts and goals

### M2: Window mode

- Choose between whole desktop and a single window
- Switch between windows from the phone
- Detailed requirements to be written before M2 starts

### M3: Control

- Control the Mac from the phone in both desktop and window modes
- Detailed requirements to be written before M3 starts

## Out of Scope

- Access outside the home network (planned for after M3)
- USB connection
- Audio streaming
- Multiple phones viewing at once
- Android or Windows support