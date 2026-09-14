# Driver Display and Bluetooth security

Driver Display combines a driving-focused interface with multiple read-only data sources.

## Data-source modes

- **Auto:** uses the freshest trustworthy source and falls back cleanly when a source becomes unavailable.
- **Direct Bluetooth:** reads compatible live driving telemetry from the selected nearby vehicle.
- **iPhone + TeslaMate:** uses iPhone location/speed with TeslaMate context and does not require vehicle Bluetooth.

GPS speed may differ slightly from the vehicle display because of sensor accuracy, filtering and the vehicle's own speedometer calibration. Tesstats offers a configurable display offset; adjust it only after comparing sustained speed under safe conditions.

## Pairing

Bluetooth pairing is per vehicle. Use the settings button in Driver Display, select the correct vehicle and follow the on-screen approval flow while close to the car. The vehicle must be awake and compatible.

If pairing fails:

1. Confirm Bluetooth is enabled for Tesstats in iOS Settings.
2. Confirm the selected vehicle matches the vehicle beside you.
3. Wake the vehicle and keep the phone nearby.
4. Remove a stale pairing in Tesstats and from the vehicle before pairing again.
5. Keep Driver Display visible while connecting; the app intentionally disconnects in the background.

## Read-only security boundary

- Tesstats acts only as a Bluetooth central and does not advertise a service or accept inbound Bluetooth connections.
- Pairing requests Tesla's **Vehicle Monitor** role, which is intended for reading telemetry and cannot authorise changes to the car.
- No unlock, drive, climate, charging or other vehicle-control operation is exposed by the app.
- Each vehicle receives a separate Secure Enclave P-256 key.
- The private key is non-exportable, stored for that iPhone only and never sent to TeslaMate, Tesla servers or another device.
- Sessions use authenticated key agreement and encrypted responses with replay protection.
- Bluetooth runs only while Driver Display is visible and stops when the app leaves the foreground.

Deleting a pairing removes the local protected key and preferences. Also remove the corresponding public key from the vehicle to revoke the vehicle-side authorisation.

Direct Bluetooth currently targets supported Model 3 and Model Y vehicles. Compatibility can vary with vehicle hardware and firmware; iOS Simulator testing cannot reproduce a real Tesla BLE connection.
