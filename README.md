# Home Assistant Blueprints

A collection of Home Assistant blueprints. Each blueprint is a single YAML file
under `blueprints/<domain>/` and can be imported on its own.

| Blueprint | Domain | Description |
| --- | --- | --- |
| [Wake-up Light](#wake-up-light) | automation | Simulates a sunrise by gradually brightening and cooling lights |
| [Attic Climate Control](#attic-climate-control) | automation | Keeps an attic cool with motorised windows and sun screens |

## Importing

Click the import badge of a blueprint, or go to
**Settings → Automations & Scenes → Blueprints → Import Blueprint** and paste
the blueprint's URL.

## Adding a blueprint

1. Create `blueprints/<domain>/<name>.yaml` (`<domain>` is `automation` or `script`).
2. Set `source_url` in the `blueprint:` header to
   `https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/<domain>/<name>.yaml`.
3. Add a row to the table above and a section below with an import badge.

---

## Wake-up Light

Gradually brightens one or more lights and warms them up to a cooler colour
temperature to simulate a sunrise.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fwakeup-light.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/wakeup-light.yaml
```

Requires Home Assistant 2024.10 or newer.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Wake-up Time | `input_datetime` helper (time) that starts the sequence | – |
| Wake-up Light(s) | One or more lights to brighten | – |
| Active Days | Days on which the automation runs | Mon–Fri |
| Transition Time | Duration of the sunrise in minutes | 30 |
| Person(s) | Only run when at least one of them is in the zone (leave empty to always run) | none |
| Zone | Zone the person(s) must be in | `zone.home` |
| Start / End Brightness | Brightness at the start and end of the sunrise | 10% / 100% |
| Start / End Color Temperature | Colour temperature at the start and end | 2202 K / 4000 K |

Turning all lights off during the sunrise stops the sequence.

---

## Attic Climate Control

Keeps an attic cool using motorised windows and sun screens, based on the attic
and outdoor temperature, the position of the sun and optional rain and wind
sensors.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fattic-climate.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/attic-climate.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

**Windows** (checked first for rain and wind)

| Situation | Windows |
| --- | --- |
| Raining, or wind at/above the window limit | Close |
| Attic at/below comfort temperature − hysteresis | Close |
| Warmer outside than in the attic | Close |
| Colder outside than the minimum outdoor temperature | Close |
| Attic at/above comfort temperature and outside at least the minimum difference cooler | Open |
| Anything in between | Leave as is |

**Screens** ("closed" means down, blocking the sun)

| Situation | Screens |
| --- | --- |
| Wind at/above the screen limit | Open (retract) |
| Sun on the screens (elevation and direction), bright enough, and attic or outside warm | Close |
| Sun not on the screens, or attic and outside cooled down by the hysteresis | Open |
| Anything in between | Leave as is |

The automation checks every 5 minutes, whenever a temperature changes, and
immediately when it starts raining or the wind picks up. Covers that are
already in the desired state are not touched, so a window you opened to a
different position stays there. Use the optional enable switch
(`input_boolean`) to pause the automation and control everything by hand.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Attic Temperature | Temperature sensor in the attic | – |
| Outdoor Temperature | Outdoor temperature sensor or weather entity | – |
| Window(s) / Screen(s) | Covers to control; either may be left empty | none |
| Enable Switch | Optional `input_boolean`; the automation only acts while it is on | none |
| Comfort Temperature | Windows open above this when it is cooler outside | 24 °C |
| Hysteresis | Cool-down needed before windows close / screens open again | 1 °C |
| Minimum Temperature Difference | How much cooler it must be outside to open windows | 1 °C |
| Minimum Outdoor Temperature | Windows stay closed below this | 12 °C |
| Window Open Position | Position to open windows to (below 100 needs position support) | 100% |
| Screen Attic Temperature | Screens close above this attic temperature when the sun is on them | 22 °C |
| Screen Outdoor Temperature | Screens also close above this outdoor temperature when the sun is on them | 25 °C |
| Minimum Sun Elevation | Sun counts as shining above this elevation | 10° |
| Sun Azimuth Start / End | Sun direction that hits the screens (180 = south) | 0° / 360° |
| Illuminance Sensor / Threshold | Optional; screens only close when brighter than this | none / 20000 lx |
| Rain Sensor | Optional binary sensor; windows close while on | none |
| Wind Speed Sensor | Optional wind speed sensor | none |
| Maximum Wind Speed for Windows / Screens | Limits in the unit of the wind sensor | 50 / 40 |
