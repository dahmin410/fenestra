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
- **FR-9:** The Mac app must only accept new pairing requests while the user has turned on pairing mode, and must show at most one pairing prompt at a time. (TH-1, TH-15)
- **FR-10:** Pairing must require entering, on the iPhone, a code shown on the Mac. (TH-1)
- **FR-11:** The Mac app must let the user choose the name it advertises, and must only advertise while sharing is turned on. (TH-10)
- **FR-12:** The Mac app must list paired devices and let the user remove any of them. A removed device must pair again to connect. (TH-3)
- **FR-13:** The iPhone app must require Face ID or the device passcode each time it is opened before connecting. Automatic reconnects in the same session must not ask again. (TH-3)
- **FR-14:** While streaming, the Mac app must show which device is connected, and show a notification when a device connects. (TH-8)
- **FR-15:** The Mac app must keep a local log of pairings, connections, disconnections, and removed devices, and let the user view it. (TH-7)

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
- **NFR-9:** Stream frames must never be written to disk or logs, and the iPhone app must hide the stream whenever it leaves the foreground. (TH-11)
- **NFR-10:** The Mac app must limit how quickly it accepts connection attempts, and must not start screen capture for a device until that device is authenticated. (TH-14)
- **NFR-11:** The Mac app must check every incoming message against what the sending device is allowed to do, and reject anything else. (TH-17)


### Compatibility

- **NFR-8:** Minimum supported macOS and iOS versions will be decided in an ADR.

## Milestones

### M0: Foundation

Project skeleton with no streaming yet.

- Xcode project with Mac and iPhone apps that build and launch
- GitHub Actions builds and tests every pull request
- Requirements, threat model, and architecture docs written

### M1: View the desktop

- FR-1 through FR-15 complete
- NFR-1 through NFR-7 and NFR-9 through NFR-11 met, with performance results recorded against their musts and goals

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