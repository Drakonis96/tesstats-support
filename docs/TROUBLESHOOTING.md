# Troubleshooting

[Back to README](../README.md) · [Setup guide](SETUP.md)

Start by checking the app version/build, selected server profile, selected vehicle and whether **Demo mode** is off. Compare with TeslaMate itself: if its data is stale, an app reconnect cannot create fresh upstream data.

## Work through the connection layers

1. Is the phone on the right Wi-Fi/VPN, and can it resolve/reach the server?
2. Is the TLS certificate valid for that hostname?
3. Does the reverse proxy accept its own credentials?
4. Does MQTT accept the separate broker credentials and permit the required topics?
5. Does the history API return vehicle JSON at the expected path?
6. Is the app looking at the correct vehicle and period?

Use **Settings → Server → Test connection**. MQTT and API diagnostics are independent.

| Symptom | Check |
|---|---|
| Timeout / server unreachable | DNS, VPN route, firewall and whether the phone can reach the server on mobile data. |
| TLS/certificate failure | Correct hostname, expiry and chain. Do not disable verification to hide an unexplained error. |
| WSS fails but native MQTT works | Proxy WebSocket support, external port and exact path; these are different transports. |
| MQTT rejects credentials | Broker username/password, not Tesla login or proxy password. |
| Connected, but no topics within the test window | Namespace, read ACL and TeslaMate publishing to this same broker. |
| API 401/403 | Proxy Basic Auth and access rules. A command Bearer token is not the app's Basic Auth setting. |
| API 404 | Base URL should normally end in `/api`, not `/api/v1`; proxy must preserve that prefix. |
| API decoding error / HTML response | The URL may point to TeslaMate, Grafana, an SSO login page or an error page instead of TeslaMateApi JSON. |
| Works on Wi-Fi only | Home-only address or VPN missing on mobile data. Do not publicly open database/MQTT ports as a shortcut. |

The default namespace is blank, giving topics under `teslamate/cars`. For a custom namespace `home`, enter only `home`, not a complete topic or wildcard.

## Live data works, history does not

MQTT feeds live values; **TeslaMateApi** feeds historical screens. Confirm its container is running against the correct database and that its authenticated endpoint returns cars. A fresh installation may have no historical journeys yet.

If only one period is empty, widen the date filter, verify the selected vehicle and let loading finish. A hidden card is restored through the four-square layout control.

## Bluetooth is paired but there is no speed

A saved Vehicle Monitor key is not the same as an active authenticated telemetry stream. Audio Bluetooth pairing is unrelated.

1. Check the selected car/masked VIN, Bluetooth permission, proximity and vehicle wake state.
2. Keep Driver Display in the foreground.
3. Check the selected mode: **iPhone + Tesstats** intentionally disconnects direct Bluetooth.
4. In **Bluetooth** mode, unavailable telemetry is not replaced with a fabricated zero. Try **Auto** or **iPhone + Tesstats** as a separate diagnostic.
5. Record whether the status is paired, reconnecting, connected, or an error.
6. Only remove/re-pair after the preceding checks; removal must be completed on both the iPhone and car.

See [pairing instructions](DRIVER_DISPLAY.md#pair-read-only-bluetooth). Real vehicle/firmware compatibility cannot be established by a simulator or documentation alone.

## GPS speed, map or zoom looks wrong

- Allow precise location while using Tesstats and test outdoors.
- Review the iPhone-only speed adjustment, enabled by default at +2 km/h.
- Note whether the issue happens at steady speed, acceleration, in a tunnel or after losing reception.
- Verify the source mode and whether map/map-zoom elements are enabled.
- Do not test by operating controls while driving. A passenger can record observations safely, or inspect while parked.

A display offset is not a fix for GPS latency. Map freshness also depends on the location stream; the Bluetooth telemetry integration does not provide map coordinates.

## Notifications only arrive after opening the app

First run a local notification test, then a remote test **only if an authorised push service is configured**. A successful local test does not verify APNs. Read [Notifications and Live Activities](NOTIFICATIONS.md) for permissions, service requirements and Sentry limitations.

If you have no compatible backend, closed-app continuous MQTT alerts are not available merely by enabling notification permission. Do not buy unrelated APNs credentials to attempt to enable them.

## Live Activity or watch data is stale

Open Tesstats on the iPhone and verify its source data first. Check phone/watch connectivity and app versions. Widgets/watch snapshots are subject to system scheduling. Live Activity background updates additionally need their own successful registration with a compatible push backend.

A refresh when the app opens is not proof of working background updates.

## A free charge still has a cost

Set the location's **price per kWh to zero**, rather than leaving it empty. Check whether TeslaMate recorded a total greater than 0.01, which has priority. A location override applies to matching unpriced history, not just one session.

Use [Charging and costs](CHARGING.md) to check tariff precedence, billable energy and currency. The current app treats missing/zero/tiny recorded costs as candidates for estimation.

## Monthly totals differ from the list or export

The monthly card has its own filters. Align location selection and inclusive date interval with the session list before comparing. Sessions are bucketed by start date; charges spanning month-end are not split between months. Check pricing changes and estimated-cost indicators.

The export uses session-list selection and current prices, not automatically the monthly card's separate filter.

## A charge curve is missing or incomplete

A summary record does not guarantee per-point samples. Check the individual session, TeslaMateApi availability and source history. A very short or slow AC session may not yield a useful curve. Exports of session summaries do not include curve samples.

## If the app crashes

Record the app build, device/OS, screen, selected source mode and exact preceding steps. Note whether it occurs in demo mode too. If reproducible, submit feedback through TestFlight or a redacted issue. Do not delete app data as the first step: it can remove local preferences and evidence. Keep configuration backups private.

## What to send to support

Search [existing issues](https://github.com/Drakonis96/tesstats-support/issues), then [open a report](https://github.com/Drakonis96/tesstats-support/issues/new/choose) with:

- App version and build; device model and iOS/iPadOS/macOS/watchOS version.
- Screen, selected mode and whether real or demo data was used.
- LAN/VPN/proxy connection type, without private addresses or credentials.
- Numbered reproduction steps, expected result and actual result.
- Approximate time/time zone and exact **redacted** error text.
- A cropped/redacted screenshot if useful; for Bluetooth, model family and firmware rather than VIN.

Never attach APNs keys, device tokens, private server configuration, backups or identifiable routes. For potentially sensitive security findings, ask for a private contact route before sharing details.
