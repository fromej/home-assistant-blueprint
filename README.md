# Home Assistant Wake-up Light Blueprint

Gradually brightens one or more lights and warms them up to a cooler colour
temperature to simulate a sunrise.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fwakeup-light.yaml)

Or import manually: **Settings → Automations & Scenes → Blueprints → Import Blueprint** and paste:

```
https://github.com/fromej/home-assistant-blueprint/blob/main/wakeup-light.yaml
```

Requires Home Assistant 2024.10 or newer.

## Inputs

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
