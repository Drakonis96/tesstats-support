# Feature guide

## Summary

The main dashboard shows current battery, estimated range, charging state, climate, locks, Sentry status, tire pressure and vehicle location. Cards can be customised without duplicating the same information in multiple tabs.

## Driver Display

Driver Display is a configurable, full-screen driving view for portrait and landscape use. It can show speed, gear, power/regen, battery and range, temperatures, odometer, current-trip distance, average trip consumption, estimated trip cost and a following map. Individual elements can be enabled or disabled from the display itself.

The display supports iPhone GPS/TeslaMate and an optional direct, read-only Bluetooth source. See [Driver Display and Bluetooth security](DRIVER_DISPLAY.md).

## Trips

Trips include route, distance, duration, energy consumption and elevation when provided by TeslaMateApi. Work/Personal tags are kept on the device and can be used for filtering and exports.

## Charging

Charging history includes energy, cost, location, AC/DC mix and charge curves when TeslaMate recorded enough per-point data. Tesstats accepts zero-cost charging and can use per-location prices or time-of-use tariffs.

## Battery health and statistics

Battery views estimate usable capacity, degradation and full-charge range from available history. Statistics include monthly comparisons, consumption, cost per distance, charging locations, temperature relationships and calendar activity. These are estimates derived from TeslaMate data rather than official battery diagnostics.

## Apple features

Depending on the device and installation method, Tesstats can provide widgets, charging Live Activities, Siri/Shortcuts and an Apple Watch companion. Apple Watch pages cover battery and charging, range, locks/Sentry/occupancy, tire pressure, climate and battery-health statistics.

## Multiple vehicles

Vehicle selection, Bluetooth pairing and display state are managed per vehicle. Verify the selected vehicle before changing connection or pairing settings.
