# Notifications and Live Activities

[Back to README](../README.md) · [Troubleshooting](TROUBLESHOOTING.md)

## Start with the important limitation

**TestFlight installation alone does not enable continuous background vehicle monitoring.** iOS can suspend Tesstats and its MQTT connection. Local alerts can work with the app open while no new alerts arrive when it is suspended.

For background delivery, a compatible always-on backend must receive TeslaMate events and send authorised Apple Push Notification service (APNs) messages. Even with that backend, delivery timing is affected by connectivity and iOS; it is not guaranteed.

## 1. Enable local alerts

1. Open Tesstats and connect to your real vehicle with demo mode disabled.
2. In **Settings → Alerts**, allow/request notification permission and enable the wanted categories.
3. In iOS **Settings → Notifications → Tesstats**, allow the desired Lock Screen, banner and sound presentation.
4. Review Focus modes, Scheduled Summary and the app's quiet hours.
5. Use the notification test in Tesstats. A local test verifies local presentation only, not server delivery.

Alerts depend on available data and the configured rules. They can include charging changes, an unlocked/open parked car, tire pressure, geofences, software updates and unpriced completed charges. A permission switch cannot produce events TeslaMate never reports.

## 2. Understand the optional push service

**Do not buy an Apple Developer membership, create arbitrary APNs keys or send anyone your Apple credentials to try the beta.**

A TestFlight build is signed for the publisher's application identity. Its push backend needs APNs authority for **that same app/team**, plus the production APNs environment. An unrelated developer account cannot send notifications to this signed app simply by creating a key.

This documentation repository does not distribute the private push-server implementation or an APNs key. If you have not been provided a compatible service through an authorised setup, leave **Immediate push** off. You can still use the app's other features, but should not expect continuous closed-app alerts.

Do not guess a service URL, point this field at TeslaMateApi or give a third-party service access to your MQTT data without understanding its trust and privacy implications.

## 3. Register when an authorised service is available

1. Obtain the **HTTPS push-service URL** and **shared registration secret** through a trusted, private channel.
2. Open **Settings → Alerts → Immediate push (optional)**.
3. Enable **Immediate push (app closed)** and enter those details.
4. Save, then wait for **Push connected**. A filled-in URL does not mean registration succeeded.
5. Tap **Send remote test**. Check arrival with the phone locked as well as with the app visible.
6. If registration or the test fails, report the redacted error. The service administrator should check MQTT connectivity, APNs credentials/app identity and production configuration.

Never publish a registration secret, APNs key, device token or server credentials. Normal users do not need to handle the publisher's signing material.

## 4. Set up a charging Live Activity

1. Enable the Live Activity option in **Tesstats Settings → Alerts**.
2. Check that iOS allows Live Activities for Tesstats.
3. During a real charging session, open Tesstats and verify battery/charging values.
4. If using an authorised push backend, check the **Background Live Activity updates connected** status too. This registration is separate from an ordinary notification token.
5. Lock the phone and compare freshness safely. A frozen percentage can be stale data, not a stopped charge.

While the app is active it can update the Live Activity locally. Background updates require the compatible backend to register and use the Activity's own token. Opening the app can refresh a stale Activity, but does not prove background delivery is working. Colours follow the app's accent preference when updates reach the Activity.

## Sentry: armed is not an event

Tesstats distinguishes an armed/disarmed state from a **possible Sentry event**. The latter is inferred from a TeslaMate-reported Sentry screen/banner signal; it is not a direct feed of every incident or recorded clip.

- Arming Sentry does not itself mean a detection took place.
- If TeslaMate does not supply the event signal, the app cannot infer that event.
- Repeated inferred events are rate-limited (currently a ten-minute cooldown).
- Quiet-hours and category preferences can also affect presentation.
- Clips stay on the car's storage; Tesstats does not retrieve them.
- The app is not a security/alarm replacement.

Use the Sentry signal diagnostics in **Settings → Alerts** to distinguish missing telemetry from a delivery problem.

## Charging-cost reminders

When the app detects a completed charge requiring a price, a reminder can lead to that charge so you can enter the location's price per kWh. Check the selected vehicle and location before saving; use zero for free charging. Detection and background delivery still depend on the data path and push setup above.

## Diagnose in order

| Symptom | First check |
|---|---|
| No local test banner | iOS permission, Focus, Scheduled Summary and sound/banner settings. |
| Local test works; remote test does not | Authorised service configuration, registration status and server/APNs diagnostics. |
| Remote test works; no vehicle alerts | Event/category rules, MQTT feed and whether a relevant change actually occurred. |
| Alerts work; Live Activity stays stale | Activity-specific registration/token and compatible backend support. |
| Notifications appear only after opening Tesstats | Foreground processing is working; background delivery is not established or did not arrive. |

After reinstalling or changing server settings, open Tesstats and save/register again. Treat alerts as a convenience, not a guaranteed safety service.
