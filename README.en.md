# SunEnergyXT 500: Zero Feed-In with Home Assistant

[Deutsch](README.md) | **English**

Control one SunEnergyXT 500 / 500 Pro or coordinate several devices together.
These blueprints adjust power using an external Home Assistant meter.

## Choose a blueprint

| Blueprint | Use case | Guide |
| --- | --- | --- |
| Single device | One SunEnergyXT 500 / 500 Pro | [Set up one device](#single-device-setup) |
| Multi-space | Up to nine devices behind one household meter | [Set up multiple devices](#multi-space-setup) |

**English import URLs:**

| Blueprint | Home Assistant import URL |
| --- | --- |
| Single device | `https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml` |
| Multi-space | `https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-multi-zero-feed-in.en.yaml` |

## Requirements

- Home Assistant 2024.6 or newer.
- The SunEnergyXT 500 / 500 Pro integration is set up.
- An external meter provides current power in Home Assistant, not only accumulated energy readings.

## Import a blueprint

In Home Assistant, open `Settings` -> `Automations & Scenes` -> `Blueprints` ->
`Import blueprint`. Paste the matching URL from the table above, then create
an automation from the imported blueprint.

Alternatively, place the selected YAML file in
`/config/blueprints/automation/sunenergyxt/` and reload the blueprint page.

## Single-device setup

Select `SunEnergyXT 500 Series - Zero Feed-in`. This blueprint adjusts one
device's grid-port power setpoint and maximum inverter power. It does not change
the device operation mode or discharge mode.

1. Select the `SunEnergyXT device`.
2. Configure SOC limits, maximum on-grid output power, and AC-coupled maximum charge power.
3. Select the full-battery behavior.
4. Configure the meter as described under [Meter configuration](#meter-configuration).
5. Save the automation disabled first and follow the first-run guidance below.

| Setting | Description |
| --- | --- |
| Full-battery behavior | `Follow load after full` or `Follow PV after full` |
| Maximum on-grid output power | 800 W for SunEnergyXT 500, 2400 W for SunEnergyXT 500 Pro, unless a lower local limit is required |
| System Min Discharge SOC | Written to the device setting and used as the lower SOC threshold |
| System Max Charge SOC | Written to the device setting and used as the upper SOC threshold |
| AC-coupled maximum charge power | Up to 2400 W per device, if permitted by the installation |

In the single-device blueprint, a 1% memory band near the upper SOC limit
prevents repeated toggling in `Follow PV after full`. If meter, GP, LP, or PV
data is temporarily invalid while the full state is confirmed, it holds PV
bypass with `GS = 0 W` and maximum `IS`. It does not force bypass when SOC, GS,
or IS is invalid.

## Multi-space setup

Select `SunEnergyXT 500 Series - Multi-Space Zero Feed-In`. One automation
coordinates up to **nine SunEnergyXT 500 / 500 Pro devices** behind one household
meter. Device models are detected through the integration; manual model
selection is not required.

Each device's grid port must connect independently behind that meter, not to
another controlled device's load port.

1. Select device 1 and up to eight additional devices; leave unused device fields empty.
2. Configure SOC limits, power limits, and full-battery behavior.
3. Configure the household meter as described under [Meter configuration](#meter-configuration).
4. Save the automation disabled first.
5. Before enabling it, disable other automations controlling the same devices, including existing single-device automations.

`Fast load-step correction (optional)` is enabled by default and bases the
correction on the total household meter deviation. Feedback settle time still
applies; actual response time also depends on the meter and devices. An explicitly
saved `off` setting remains unchanged.

## Meter configuration

Meter configuration applies to both blueprints. Expand and fill only the section
matching your meter type; leave the other sections empty.

| Meter type | Configuration |
| --- | --- |
| Shelly Pro 3EM | Select the meter type and meter device |
| EcoTracker | Select the meter type and meter device |
| BitShake / Tasmota | Select the meter type and meter device |
| Shelly 3EM | Select the three phase devices L1 / L2 / L3; their power readings are summed |
| Custom meter | Choose the power formula and provide the matching entities |

The sign setting must match the actual meter reading:

- If the meter shows export as positive, select the export-positive option.
- If the meter shows grid import as positive, select the import-positive option.
- Use multiplier `1` for readings in W, or `1000` for kW.

## First run and notes

- Confirm that the required device and meter entities are not `unknown` or `unavailable`.
- Enable only one power-control automation per device to avoid conflicting commands.
- The default target is `0 W` at the external meter. After enabling the automation, check that the meter moves toward the target; the selected follow-PV mode can allow export at full battery.
- If control moves in the wrong direction, check the meter sign setting.
- If response is slow, check the meter update rate, feedback settle time, and control intervals. A short write interval alone does not guarantee an equally fast response.
- Diagnostic logging is off by default. Enable it only when needed.
