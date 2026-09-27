# Driver Display

[Back to README](../README.md) · [Troubleshooting](TROUBLESHOOTING.md)

A customisable, read-only cockpit for portrait and landscape. Set it up **while parked**, keep the phone securely mounted and use the Tesla's instruments and warnings as the primary source.

## Open and customise

1. Select the correct vehicle in Tesstats, then open Driver Display.
2. Use the **four-square settings button** near the upper-right close button to open its settings (older screenshots may show a gear).
3. Choose a telemetry source and configure speed adjustment there.
4. In that same settings sheet, configure visible display elements under **Modules**. Individual switches replace fixed layout presets; the display adapts to what remains visible.
5. Use the day/night button beside the layout control to change the driving appearance.
6. Use `+` / `−` at the lower right to zoom around the vehicle marker. Enable the map and map-zoom elements if they were hidden.
7. Use **X** to leave Driver Display. Configure **Keep screen awake** if desired; this can increase power use.

Available elements include speedometer, gear, speed activity, battery, range, power/regeneration, consumption, trip cost/distance, odometer, inside/outside temperatures, map, route information when available, alerts, time, logo and connection status. Visibility does not make unavailable telemetry appear.

## Choose a source

| Mode | Speed source | Position and behaviour |
|---|---|---|
| **Auto** | Stable direct Bluetooth when available, with fallback to iPhone/TeslaMate | Designed to avoid rapid source switching; availability and freshness still matter. |
| **Bluetooth** | Vehicle Bluetooth speed only | Map still uses iPhone location: this Bluetooth integration does not supply vehicle coordinates. No telemetry can mean an unavailable speed reading, not zero speed. |
| **iPhone + Tesstats** | iPhone GPS, with TeslaMate vehicle-data fallback | Uses iPhone position; direct Bluetooth stays disconnected. |

The car's ordinary Bluetooth audio/phone pairing is **not** Tesstats telemetry pairing. A stored read-only key is also not proof that fresh data is arriving.

## iPhone speed and location

1. Enable Location Services and allow Tesstats location access while using the app.
2. Enable **Precise Location** for Tesstats in iOS Settings.
3. Check outdoors, with a clear satellite view and the phone in a stable mount.
4. In Driver Display settings, review **Speed adjustment → iPhone GPS correction**.

The display adjustment is enabled by default at **+2 km/h**, with a configurable range of **−10 to +10 km/h**. Disable it for an unadjusted GPS reading. It affects only iPhone GPS speed, not vehicle Bluetooth/TeslaMate readings.

GPS speed and a vehicle speedometer need not match, particularly during acceleration, in tunnels or with poor reception. An offset changes the display; it does not improve GPS accuracy or remove latency. Do not calibrate it by interacting with the app while driving.

## Pair read-only Bluetooth

Initial support targets compatible **Model 3 and Model Y** vehicles and a physical iPhone. Firmware/hardware can affect compatibility. No separate Tesla developer account is needed for the pairing flow.

1. Park safely, wake the car and bring the iPhone close to it. Have an authorised Tesla key card available.
2. Select that vehicle in Tesstats. The app obtains its VIN from the configured vehicle data; confirm the masked VIN/name in the pairing sheet.
3. Enable Bluetooth and allow Tesstats Bluetooth access in iOS Settings.
4. Open Driver Display → four-square settings button. Choose **Auto** or **Bluetooth**.
5. Tap **Pair read-only Bluetooth** and keep the sheet open while the app discovers the selected vehicle.
6. When prompted, place the key card at the vehicle's designated reader and approve the request on the Tesla screen.
7. Wait for authentication and telemetry to complete. Approval alone is not the end of the flow.
8. Confirm the connection status and that plausible fresh values appear. Repeat separately for each vehicle you wish to use.

If discovery fails, check the selected VIN, proximity, permissions and whether the car is awake. Do not repeatedly delete working keys as a first troubleshooting step. If pairing exists but values do not arrive, record the exact state/error and test the iPhone source independently.

## Disable or remove pairing

- Turn **Use direct Bluetooth** off to keep a pairing stored but disable its use.
- Choose **iPhone + Tesstats** to use that source without a direct Bluetooth connection.
- For permanent removal, tap **Remove pairing → Remove from this iPhone**. This destroys the local protected key.
- Finish revocation on the Tesla screen: **Controls → Locks**, then remove the corresponding read-only key. Deleting locally does not delete the vehicle-side entry.
- A new phone needs its own pairing; the protected private key is not transferred in a configuration backup.

Bluetooth telemetry is foreground-only. Closing Driver Display or backgrounding the app stops its connection. This is expected, not evidence that the key was lost.

## Interpret the readouts

- **This trip consumption** is the cumulative average since the current trip started, not instantaneous power. It depends on sufficient distance/energy data.
- **Trip cost** estimates the consumed energy's value using available charging history and prices; incomplete history reduces accuracy.
- **Available / full-charge range** is an estimate, not a guaranteed remaining driving distance.
- **Int. / Ext.** refer to the vehicle's reported temperatures, not the iPhone's temperature.
- A dash means a value is unavailable; do not read it as zero.
- Map/route presentation is supplemental and is not a promise of turn-by-turn guidance.

## Read-only security boundary

The app acts as a Bluetooth central; it does not advertise an inbound control service. Pairing requests Tesla's **Vehicle Monitor** role. The integration reads driving telemetry and exposes no unlock, drive, climate or charging-control actions.

Each vehicle has a separate Secure Enclave-backed private key, kept on that iPhone and not exported to the server. Authenticated sessions, encrypted responses and replay checks protect telemetry. This is a security design, **not a claim of zero risk** on every device or firmware. Report problems without sharing VINs, keys or raw pairing material.
