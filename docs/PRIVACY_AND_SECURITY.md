# Privacy and security

[Back to README](../README.md)

## Data paths

Core vehicle data comes from the TeslaMate MQTT broker and history API you configure. Optional direct Bluetooth provides nearby driving telemetry. Optional push involves the configured backend and Apple APNs, and map services involve Apple. Do not interpret “your infrastructure” as a promise that no platform network services are used.

Only configure servers you operate or trust: they can hold sensitive routes, vehicle identifiers and activity history.

## Credentials and backups

Passwords/authentication secrets use Apple Keychain; non-secret preferences are stored locally. Encrypted configuration backups are protected by your chosen password. A backup or data export can contain identifying information even if no password is visible.

Do not share an unredacted backup, server configuration, full log or export in a public issue. Bluetooth private keys are protected on the original iPhone and are not a portable backup item.

## Read-only is an application boundary

Tesstats provides monitoring, not vehicle-control actions. Its optional Bluetooth flow requests the Vehicle Monitor role; see [Driver Display](DRIVER_DISPLAY.md).

Separately secure the services it connects to. TeslaMateApi has its own optional command features: keep them disabled for a Tesstats-only setup. Protect read endpoints too, and give the app's MQTT account only read permissions to the required topics. A read-only app is not a substitute for a hardened server.

## TLS and certificate settings

Leave the defaults enabled and use valid trusted certificates wherever possible.

- **Custom/self-signed certificate trust:** when enabled without a pin, the current implementation can accept a connection even when normal certificate validation fails. It does not import a particular CA or remember an individually approved certificate. Do not use this switch to dismiss an unknown certificate warning on an untrusted network.
- **Pinned public-key SHA-256:** advanced configuration. The app expects base64-encoded SHA-256 of the leaf key's external representation used by Apple's Security framework. A certificate fingerprint or a generic SPKI hash may not match this format. Verify the value independently; key rotation requires updating the pin. A configured pin takes precedence over ordinary certificate validation.
- **Allow unencrypted (LAN only):** opt-in for HTTP history access. The label does not constrain routing to a LAN or encrypt data. Credentials may travel in plaintext when that override is enabled. It does not turn MQTT into plaintext MQTT.

Do not weaken trust as a general troubleshooting technique. Fix the hostname, certificate chain/expiry or endpoint first.

## Bluetooth protection

The app is a Bluetooth central, not an inbound control service. Pairing uses a separate protected key per vehicle, with vehicle-side approval. The implementation authenticates sessions and checks encrypted responses/replays.

No implementation can honestly promise zero risk. Keep iOS and the car updated, protect access to your phone, and remove the corresponding key from both the app and vehicle when revoking access.

## Safe support reports

Before sharing, remove:

- VINs, registration plates, vehicle identifiers and names;
- home/work locations, route coordinates and map details;
- identifying server addresses and usernames;
- passwords, bearer tokens, Authorization headers and registration secrets;
- APNs keys, device tokens and Bluetooth pairing material.

Use [issues](https://github.com/Drakonis96/tesstats-support/issues) for redacted reproduction steps. For a security concern, describe the issue without exploit-sensitive private data and request a private contact method before disclosing details.
