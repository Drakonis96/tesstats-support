# Notifications and Live Activities

## Why background updates need a server

iOS does not allow Tesstats to keep MQTT running continuously while the app is closed or suspended. Local alerts and Live Activity refreshes therefore cannot be guaranteed from the app alone.

For timely alerts with Tesstats closed—including possible Sentry events—a small always-on push bridge must listen to TeslaMate MQTT and send Apple Push Notification service (APNs) messages. The same bridge can update charging Live Activities while iOS suspends the app.

## Requirements for TestFlight installs

- Tesstats must have notification permission in iOS Settings.
- The push bridge needs an Apple APNs authentication key and must use the **production** APNs environment for TestFlight.
- The app must be opened after installation or an environment change so it can register its current device token.
- In **Settings → Alerts**, enable immediate push, enter the push-service URL and shared registration secret, then use the remote notification test.
- The server must remain connected to MQTT and reachable over HTTPS.

Never publish an APNs `.p8` file, Key ID, registration secret, MQTT credentials or device token in this repository or in an issue.

## Notification types

Depending on configuration and available TeslaMate data, Tesstats can alert for charging state and target, possible Sentry activity, an unlocked or open vehicle while parked, geofence transitions, software updates, low tire pressure and completed charging sessions that need a price.

Possible Sentry alerts are inferred from TeslaMate state. TeslaMate does not expose the Sentry video clip, which remains on the vehicle's USB storage.

## Live Activities

A charging Live Activity has its own short-lived push token. Tesstats registers this token with the push bridge so it can receive battery, power, range and ETA updates in the background. Open the app after an update if a Live Activity remains stale.

## Diagnostic checklist

1. Confirm notifications are allowed for Tesstats, including Lock Screen and banners.
2. Check Focus/Sleep modes and Scheduled Summary.
3. Run both the local and remote test in **Settings → Alerts**.
4. Confirm the push service reports MQTT connected, APNs configured and the production environment.
5. Confirm the app successfully registered its current device token.
6. Verify the relevant alert category is enabled for the selected vehicle.
7. If only foreground alerts work, the push bridge or APNs registration is the likely missing piece.
