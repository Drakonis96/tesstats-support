# Feature guide

[Back to README](../README.md)

Use the correct vehicle and disable demo mode before interpreting any numbers as your own. Labels below use the English interface; select English or Spanish under **Settings → Prefs**.

## Summary

Summary combines live vehicle information with recent history. Battery, estimated range, state, map, temperatures, security indicators and tire pressure depend on what TeslaMate supplies. The last-trip card requires history.

Use the four-square layout control to show/hide and reorder supported cards. A hidden card is not missing data. Security icons are readouts, not commands to lock the car, enable Sentry or control windows.

## Driver Display

Open it while parked for a portrait/landscape cockpit with configurable elements, day/night appearance, zoom and optional Bluetooth telemetry. Configure this view from its own controls, not only general Settings. See the [Driver Display guide](DRIVER_DISPLAY.md) for each source and the pairing flow.

## Trips

1. Open Trips and allow history to load.
2. Filter the period or search available trip information.
3. Open a trip to inspect its endpoints, route and available distance, duration and consumption.
4. Use Work/Personal tags where needed; these are app-side annotations, not edits to TeslaMate's database.
5. Use export/share for the selected records.

A short or incompletely recorded drive may lack route or energy samples. Missing values should not be interpreted as zero consumption.

## Charging

Open a session for its location, energy, cost details and available curve. The Charging screen also provides AC/DC breakdown, locations and monthly cost analysis.

Use the [charging guide](CHARGING.md) for prices, free charging, tariffs and multi-location date filters. Use [export details](CHARGING_EXPORTS.md) when taking CSV/JSON/GPX data into another tool.

## Battery and statistics

Battery-health and full-charge-range estimates depend on historical observations. Small samples, temperature and driving conditions can affect results. These numbers are not a Tesla service diagnostic or warranty assessment.

Other statistical views summarise the recorded trips/charges. Verify the selected car, date range, units and pricing before comparing totals between screens.

## Settings navigation

| Tab label | Main contents |
|---|---|
| **Server** | MQTT, history API, proxy authentication, certificates and connection test. |
| **Prefs** | Appearance, accent colour, language, units, currency, electricity pricing and tariffs. |
| **Alerts** | Permissions, categories, Sentry diagnostics, quiet hours, Live Activity and optional push. |
| **Data** | Server profiles, configuration backup/restore, export and local storage. |
| **Info** | Vehicles, demo mode and app information. |

Save connection changes before retesting the dashboard. In multi-server setups, verify both the active profile and vehicle.

## Apple Watch

1. Install the watch companion from the iPhone's Watch app when offered by the installed build.
2. Open Tesstats on the phone, connect successfully and select the car.
3. Keep the paired phone/watch connection available and open Tesstats on the watch.
4. Swipe horizontally through battery/charging and range, lock/Sentry/occupancy, tire pressures, climate, and battery-health/statistics pages.
5. If the watch is stale, reopen the phone app and check its connection before retrying.

The watch receives snapshots through the iPhone integration. It does not maintain its own direct Tesla telemetry session. Background scheduling and phone availability can delay updates; it is not a guaranteed real-time safety display.

## Widgets and Live Activities

Add a Tesstats widget through the system widget picker after opening and configuring the app. Widgets use shared snapshots and OS-controlled refresh opportunities, not a permanent live connection.

Charging Live Activities are opt-in under Alerts and have separate background push requirements. See [Notifications](NOTIFICATIONS.md). Do not assume a frozen widget/Activity means the car stopped charging.

## Configuration backups and exports

In **Settings → Data**:

1. Choose **Export encrypted backup**.
2. Set a strong password and save the file somewhere private.
3. Keep that password securely; it is required to decrypt the backup.
4. To restore, choose **Restore from backup file**, select it, enter its password when prompted and review the restored configuration.
5. Test the connection and selected vehicle after restoration.

A configuration backup is not a backup of TeslaMate's PostgreSQL database. It does not transfer a Secure Enclave Bluetooth private key to another phone. Data exports are separate and may include precise routes/charging locations; do not post them unredacted.

Deleting local cache is also different from deleting server history. Read confirmation dialogs before clearing data.
