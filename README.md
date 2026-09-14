# Tesstats Support & Documentation

**A read-only Tesla statistics app powered by your own [TeslaMate](https://github.com/teslamate-org/teslamate) server.**

Tesstats brings live vehicle status, trips, charging sessions, battery health, Driver Display, widgets, Live Activities and an Apple Watch companion to iPhone, iPad and Mac. It reads data from infrastructure you control and never sends vehicle-control commands.

<p align="center">
  <img src="docs/images/dashboard.png" width="220" alt="Tesstats dashboard">
  <img src="docs/images/charging.png" width="220" alt="Tesstats charging statistics">
  <img src="docs/images/trips.png" width="220" alt="Tesstats trips">
</p>
<p align="center">
  <img src="docs/images/driver-landscape.png" width="640" alt="Tesstats Driver Display in landscape">
</p>

## Get help

- [Open a support request or report a bug](https://github.com/Drakonis96/tesstats-support/issues/new/choose)
- [Browse existing issues](https://github.com/Drakonis96/tesstats-support/issues)
- [Public website](https://drakonis96.github.io/tesstats/)
- TestFlight public beta: **coming soon**

When reporting a problem, include the Tesstats version, iOS/macOS/watchOS version, device model, the affected screen and exact steps to reproduce it. Never post passwords, API tokens, private URLs, VINs, precise locations or screenshots containing personal data.

## Documentation

- [Setup guide](docs/SETUP.md) — connect TeslaMate MQTT and TeslaMateApi securely.
- [Feature guide](docs/FEATURES.md) — what every major area of Tesstats provides.
- [Driver Display and Bluetooth security](docs/DRIVER_DISPLAY.md) — data sources, pairing, fallback and the read-only security boundary.
- [Notifications and Live Activities](docs/NOTIFICATIONS.md) — why background updates need a push bridge and how to diagnose them.
- [Privacy and security](docs/PRIVACY_AND_SECURITY.md) — where data and secrets are stored and what the app can access.
- [Troubleshooting](docs/TROUBLESHOOTING.md) — common connection, Bluetooth, notification and Apple Watch problems.

## What you need

- A Tesla already connected to TeslaMate.
- An always-on server running TeslaMate and its MQTT broker.
- TeslaMateApi is strongly recommended for trips, charging history and analytics.
- iOS 18 or later, iPadOS 18 or later, or macOS 15 or later.

You do not need a Tesla developer account, a Fleet API key or a paid cloud service for the core app.

## Privacy at a glance

- **No vehicle control:** Tesstats is a monitoring client. Its optional Bluetooth integration uses Tesla's Vehicle Monitor role.
- **Your infrastructure:** vehicle data goes between Tesstats and the server addresses you configure.
- **Encrypted by default:** secure MQTT and HTTPS are the defaults; unencrypted HTTP must be explicitly enabled for a trusted LAN.
- **Keychain storage:** credentials are stored in the Apple Keychain, not in plain-text configuration files.
- **No secrets in support:** this repository contains documentation and issue tracking only—no Tesstats source code or credentials.

Tesstats is an independent project and is not affiliated with, endorsed by or sponsored by Tesla, Inc. or the TeslaMate project. Tesla and TeslaMate are trademarks of their respective owners and are referenced only to describe compatibility.
