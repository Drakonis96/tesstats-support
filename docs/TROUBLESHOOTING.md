# Troubleshooting

## MQTT does not connect

- Confirm TeslaMate and Mosquitto are running and the selected host is reachable from the device.
- Check transport, port and WebSocket path; `mqtts` and `wss` are not interchangeable.
- Verify the Mosquitto username/password and any separate reverse-proxy Basic Auth credentials.
- For a self-signed certificate, enable custom-certificate trust only for your own certificate.
- Check firewall rules and certificate hostname/expiry.

## Trips or charging history are empty

Live data comes from MQTT, but history requires TeslaMateApi. Test the API URL from Tesstats and confirm the reverse proxy forwards the expected `/api` path.

## Bluetooth stays paired but not connected

Bluetooth is foreground-only and may disconnect whenever Driver Display is closed or the app is backgrounded. Ensure the correct vehicle is selected, the car is awake and nearby, and no stale key remains on either side. See [Driver Display](DRIVER_DISPLAY.md).

## Speed is delayed or differs from the car

In iPhone mode, grant precise location permission and test outdoors with a clear satellite view. Avoid judging the reading during rapid acceleration or in tunnels. Check the configurable speed offset in Driver Display settings.

## Notifications arrive only while the app is open

That indicates local monitoring is working but continuous background push is not. Review [Notifications and Live Activities](NOTIFICATIONS.md), especially APNs production mode and device-token registration.

## Live Activity or Apple Watch data is stale

Open Tesstats on the iPhone to refresh connectivity and registration. Confirm the watch and phone are connected and using the same current app build. Live Activities require a working push bridge for reliable background updates.

## A charge curve looks incomplete

Charge curves depend on TeslaMate's recorded per-point samples. Very short sessions, slow AC charging or missing history may not provide a meaningful curve. Confirm TeslaMateApi is current and accessible.

## Reporting an issue

Search [existing issues](https://github.com/Drakonis96/tesstats-support/issues) first. If no issue matches, include version/device details, reproducible steps, expected versus actual behaviour, and a redacted screenshot when useful.
