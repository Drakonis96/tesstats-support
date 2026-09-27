# Charging, prices and monthly costs

[Back to README](../README.md) · [Export details](CHARGING_EXPORTS.md)

## Before calculating costs

Connect TeslaMateApi, select the correct car and let charging history finish loading. In **Settings → Prefs**, set your currency and default electricity price **per kWh**. Changing the currency label does not convert historical amounts using exchange rates.

Charging cost is what it costs to replenish the battery; a trip's estimated energy cost is a different calculation. Grid energy includes charging losses, whereas energy added to the battery does not.

## 1. Set a price for a charging location

1. Open **Charging** and select a session at that location.
2. Tap **Set price per kWh** (or **Edit price per kWh**) in its cost section. An unpriced list row may also offer **Add price**.
3. Enter the unit price, for example `0.20` for €0.20/kWh, then save.
4. Check the location name: this is a **location-level rate**, not an edit to one session's total or TeslaMate's database.
5. Recheck the session and the monthly total.

The rate is matched by location name and can reprice historical unpriced sessions at that name. If you need dated historical rates, do not assume changing a location price preserves a history of previous prices. Correct existing recorded session costs in the source system as appropriate.

### Free charging

Enter **0** explicitly for the location. An absent price and a zero price are not the same thing. If TeslaMate already recorded a usable paid total, that recorded total retains priority; setting the location to zero does not erase it.

To remove a saved location override, open the session's **Edit price per kWh** dialog, clear the field and save. The session then falls back to the next applicable pricing source.

## 2. Configure a time-of-use plan

1. Open **Settings → Prefs** and its tariff-plan controls.
2. Create/edit a named plan with the applicable time bands and **buy price per kWh**.
3. Check the day coverage, time zone assumptions and default price for uncovered periods.
4. Select the plan and enable time-of-use pricing.
5. Remove any location override if you want that location's unpriced sessions to use the plan instead.
6. Inspect a charge that spans a price boundary and compare the resulting estimate with your tariff.

The calculation time-weights prices over the session and assumes energy was distributed evenly over it. Real charging power can vary, so it is not an exact electricity bill. Exports retain the applied bands and pricing time zone for inspection.

## 3. Understand the priority order

| Priority | Source | Behaviour |
|---|---|---|
| 1 | Recorded TeslaMate total | The current pricing engine uses a finite recorded total **greater than 0.01**. |
| 2 | Saved location rate | Includes an explicit zero/free rate. |
| 3 | Enabled tariff plan | Applies the time-weighted buy rate. |
| 4 | Default price per kWh | Fallback when the above are absent. |

**Important:** a recorded `0`, `0.01` or missing total currently falls through to estimation. This is why explicitly setting a free location matters. These are the current app rules, not a claim that TeslaMate can never represent a genuinely free session.

Price-based estimates use valid grid energy drawn when available (including losses); otherwise they use energy added to the battery.

Examples, assuming 20 kWh of billable energy:

- Location rate €0.20/kWh → **€4.00**.
- Explicit location rate €0/kWh → **€0.00**, if no higher-priority recorded total exists.
- Recorded session total €7.50 → **€7.50**, even with a €0.20/kWh location rate.
- No recorded/location rate; an evenly time-weighted tariff averaging €0.15/kWh → **€3.00 estimated**.

Totals can change when you edit pricing because unpriced history is evaluated with current settings. Do not use estimated figures as tax/accounting evidence without checking the original invoices.

## 4. Filter monthly charging costs

1. Find the **Monthly charging cost** card in Charging. If hidden, restore it using the section's four-square layout button.
2. Tap its filter button.
3. Choose **Days**, **Months** or **Years** and set **From** and **To**.
4. Search for a location and select one or several. Search only narrows the picker; it does not itself select a place.
5. Choose **All locations** to clear place selections. No selected places means all, not none.
6. Tap **Apply**. An end before the start must be corrected first.
7. Inspect the period total, session count and monthly bars. Select a bar for the month amount; scroll horizontally for longer ranges.

Boundaries are inclusive for the selected day/month/year using the device's calendar. Sessions are assigned by **start date**: one crossing midnight/month-end belongs to the month in which it began. A partial-month interval includes only matching sessions, not every session in that month. Zero-activity months remain on the time axis.

These filters belong to this card and are **independent of the session-list filters**. An “Includes estimated costs” notice means at least one session used price-based estimation.

## 5. Inspect charge curves and AC/DC mix

Open a charge's detail to inspect available per-point samples. A low-power AC session can look very different from a fast DC session; absent samples cannot be reconstructed from totals alone.

The Charging screen's AC/DC breakdown uses the available session classification. Check the active filters before comparing it with another summary. An incomplete history produces incomplete statistics.

## 6. Export charging records

1. Apply the desired filters to the **main Charging session list**. Monthly-cost-card filters do not automatically become export filters.
2. Tap the share/export button.
3. Select the available format and save/share the file.
4. For spreadsheets use CSV; for structured records and pricing metadata use JSON; for map waypoints use GPX.
5. Review the destination and file contents before sharing: exports can expose locations, coordinates and timestamps.

Exports use the same effective pricing engine as the UI and retain the original recorded amount separately. They contain session summaries, not every charge-curve sample. See [Charging exports](CHARGING_EXPORTS.md) for fields and estimation flags.
