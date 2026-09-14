# Privacy and security

## Data flow

Tesstats connects to the TeslaMate MQTT broker, TeslaMateApi and optional push service addresses entered by the user. Core vehicle data is not routed through a Tesstats-operated analytics cloud.

## Credentials

Passwords and authentication material are stored in the Apple Keychain. Encrypted configuration backups are protected with the password chosen by the user. Never attach an unredacted backup to a public issue.

## Vehicle access

Tesstats is read-only. MQTT and TeslaMateApi provide monitoring data. Optional direct Bluetooth uses Tesla's Vehicle Monitor role and intentionally excludes vehicle-control operations. See [Driver Display and Bluetooth security](DRIVER_DISPLAY.md).

## Network security

Secure MQTT (`mqtts` or `wss`) and HTTPS should be used outside a trusted LAN. Support for a self-signed certificate or unencrypted LAN API is opt-in so that users can connect to infrastructure they operate themselves.

## Safe support reports

Before sharing logs or screenshots, remove:

- VINs and vehicle identifiers;
- home, work and precise map locations;
- server hostnames or private IP addresses when identifying;
- usernames, passwords, tokens and HTTP Authorization values;
- APNs keys, device tokens and pairing material;
- names, registration plates and other personal information.

Use the [public issue tracker](https://github.com/Drakonis96/tesstats-support/issues) for reproducible bugs and feature requests, but never for secrets or private account data.
