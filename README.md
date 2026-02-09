# Unity Nearby Connections Transport — Documentation

This folder contains the official documentation for **Unity Nearby Connections Transport**, a custom transport for **Netcode for GameObjects (NGO)** built on top of **Google Nearby Connections**.

These documents describe installation, usage, design intent, platform notes, and known limitations.

---

## Installation

### OpenUPM (recommended)

```bash
openupm add com.bernatrosello.nearby-connections-transport
```

When installed through OpenUPM, all required dependencies are resolved automatically.

---

### Manual / Git installation

If you clone or embed this repository manually, you **must also install EDM4U**.

#### Required dependency: EDM4U

**External Dependency Manager for Unity (EDM4U)** is required to resolve and fetch:

- Google Play Services
- The **Nearby Connections API** used internally by this transport on Android

EDM4U is responsible for generating the correct Gradle dependencies so the Nearby Connections API is included at build time.

Install EDM4U via OpenUPM:

```bash
openupm add com.google.external-dependency-manager
```

Or from GitHub:
https://github.com/googlesamples/unity-jar-resolver

Without EDM4U, Android builds will fail or the Nearby API will be missing at runtime.

---

## Quick Start

1. Install **Netcode for GameObjects**
2. Install **Unity Nearby Connections Transport**
3. Add `NBCTransport` to your scene
4. Assign it as the Transport in `NetworkManager`
5. Configure Host / Client options

```csharp
using Netcode.Transports.NearbyConnections;
```

---

## Connection Model

This transport uses **Google Nearby Connections** to establish peer-to-peer links over:

- Bluetooth
- Wi‑Fi Direct
- Nearby Wi‑Fi

It is designed for **local / offline networking** scenarios.

### Strategies

- **P2P Star** — One host, many clients (recommended for NGO)
- **P2P Cluster** — Mesh-like topology
- **P2P Point-to-Point** — Single high-bandwidth link

### Event-driven design

This transport is **purely event-driven**:

- No socket polling loop
- `PollEvent()` always returns `Nothing`
- Native callbacks are marshalled onto Unity’s main thread

NGO still receives standard `Connect`, `Disconnect`, and `Data` events.

---

## Android Notes

### Permissions

The following permissions are required and requested at runtime:

- Bluetooth Scan / Connect / Advertise
- Nearby Wi‑Fi Devices
- Location (required by Nearby Connections)

Permission handling is automatic on Android builds.

### Build system

- Java bridge compiled as an Android library
- Native C++ plugin accessed via P/Invoke
- Google Play Services resolved through EDM4U

---

## Performance Notes (Initial Release)

This is the **first public release** of the transport.

### Observed performance

Initial testing shows:

- **~160 ms mean ping** in typical local scenarios
- Performance varies based on:
  - Connection strategy
  - Radio technology selected by Nearby
  - Device hardware

These values are **expected** for Nearby Connections, which prioritizes reliability and discovery over raw latency.

### Profiling tools

You can profile runtime behaviour using:

- Unity's [Multiplayer Tools - Runtime Network Stats Monitor](https://docs.unity3d.com/Packages/com.unity.multiplayer.tools@2.2/manual/runtime-stats-monitor.html)

For deeper analysis of the underlying Nearby Connections API, a dedicated performance report was conducted using a custom release of:

https://github.com/BernatRosello/AtomD/

This external tool focuses on evaluating Nearby Connections behaviour independent of NGO.

More performance tuning and benchmarks are planned for future releases.

---

## Limitations

- Android is the primary supported platform
- iOS / visionOS bindings are stubs, which contributors might want to submit PRs for, a good starting point might be found in the work being done over at the [google/nearby/connections repo](https://github.com/google/nearby/tree/main/connections). Though this isn't the actual implementation of Nearby Connections that runs natively on Android (closed source), it does presumptuously share interfaces with it. In fact a fork of this repo was used to develop the C++ JNI bindings that call into the underlying java code used on this package, [those](https://github.com/BernatRosello/NBC/tree/main/connections/unity) might be of help too.

- Throughput and latency are constrained by Nearby Connections
- Not intended for large-scale or internet-based multiplayer

---

## Intended Use Cases

- Local multiplayer games
- Party / couch co‑op
- Device‑to‑device discovery
- Offline networking scenarios

---

## License

MIT License

---

## Status

This transport is under active development.

Feedback, issues, and contributions are welcome.

