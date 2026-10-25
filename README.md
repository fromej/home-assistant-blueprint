# Home Assistant Blueprints

A collection of Home Assistant blueprints. Each blueprint is a single YAML file
under `blueprints/<domain>/` and can be imported on its own.

| Blueprint | Domain | Description |
| --- | --- | --- |
| [Wake-up Light](#wake-up-light) | automation | Simulates a sunrise by gradually brightening and cooling lights |
| [Attic Climate Control](#attic-climate-control) | automation | Keeps an attic cool with motorised windows and sun screens |
| [Sun Screen Control](#sun-screen-control) | automation | Keeps a room cool by lowering sun screens when the sun is on them |
| [Motion Lights](#motion-lights) | automation | Motion-activated lights with night mode, illuminance check and manual override |
| [Appliance Finished](#appliance-finished) | automation | Notifies and reminds you when the washer, dryer or dishwasher is done |
| [Smart Appliance Start](#smart-appliance-start) | automation | Starts the dishwasher or washer on solar surplus or in the cheapest hours |
| [Smart Load Switch](#smart-load-switch) | automation | Runs an EV charger, boiler or plug on solar surplus, cheap hours or low prices |

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

---

## Sun Screen Control

Keeps a room cool by closing sun screens when the sun shines on them and the
room (or the outside) is warm. A simpler, screens-only version of the attic
blueprint, for any floor or room.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fsun-screens.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/sun-screens.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

"Closed" means down, blocking the sun.

| Situation | Screens |
| --- | --- |
| Wind at/above the limit | Open (retract) |
| Sun on the screens (elevation and direction), bright enough, and room or outside warm | Close |
| Sun not on the screens, or room and outside cooled down by the hysteresis | Open |
| Anything in between | Leave as is |

The automation checks every 5 minutes, whenever the room temperature changes,
and immediately when the wind picks up. Screens that are already in the desired
state are not touched. Screens on different sides of the house see the sun at
different times, so create one automation per side, each with its own sun
azimuth range (for example 90–200 for east to south, 200–300 for west). Use the
optional enable switch (`input_boolean`) to pause the automation.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Room Temperature | Temperature sensor in the room behind the screens | – |
| Screen(s) | Sun screens to control | – |
| Outdoor Temperature | Optional outdoor sensor or weather entity | none |
| Enable Switch | Optional `input_boolean`; the automation only acts while it is on | none |
| Room Temperature Threshold | Screens close above this room temperature when the sun is on them | 22 °C |
| Outdoor Temperature Threshold | Screens also close above this outdoor temperature when the sun is on them | 25 °C |
| Hysteresis | Cool-down needed before the screens open again | 1 °C |
| Minimum Sun Elevation | Sun counts as shining above this elevation | 10° |
| Sun Azimuth Start / End | Sun direction that hits the screens (180 = south) | 0° / 360° |
| Illuminance Sensor / Threshold | Optional; screens only close when brighter than this | none / 20000 lx |
| Wind Speed Sensor | Optional wind speed sensor | none |
| Maximum Wind Speed | Screens retract at or above this, in the unit of the wind sensor | 40 |

---

## Motion Lights

Turns lights on when motion is detected and off when the room has been empty
for a while, dimmer and warmer at night.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fmotion-lights.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/motion-lights.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

- Motion while the lights are off turns them on, at the night settings between
  Night Start and Night End and at the day settings otherwise. With an
  illuminance sensor they only turn on when it is darker than the threshold.
- When all motion sensors are off for the turn-off delay, the lights turn off.
  New motion restarts the delay. Occupancy and presence (mmWave) sensors keep
  the lights on as long as they detect someone.
- **Manual override.** A change you make yourself (wall switch, app, voice)
  cancels the turn-off timer. With an override helper (an `input_boolean`,
  one per automation) the automation also ignores motion until all lights
  are off again, whoever turns them off. Changes made by other automations
  and scenes do not count as manual.

Dimming with a physical dimmer that HA cannot tell apart from a device report
only counts as manual when it turns the lights on or off.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Motion Sensor(s) | Motion, occupancy or presence binary sensors | – |
| Light(s) | Lights to control | – |
| Turn-off Delay | How long the room must be empty | 2 min |
| Enable Switch | Optional `input_boolean`; motion only works while it is on | none |
| Override Helper | Optional `input_boolean` that remembers a manual change | none |
| Day Brightness / Color Temperature | Light settings outside night hours | 100% / 4000 K |
| Transition | Fade time | 1 s |
| Night Start / End | Night hours | 23:00 / 06:30 |
| Night Brightness / Color Temperature | Light settings at night | 10% / 2200 K |
| Illuminance Sensor / Threshold | Optional; only turn on when darker than this | none / 30 lx |

---

## Appliance Finished

Notifies your phone when an appliance is done, detected from its power use,
and keeps reminding you until it is emptied.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fappliance-finished.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/appliance-finished.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

1. Power above **Start Power** for **Start Duration** means the appliance is
   running.
2. Power below **Finish Power** for **Finish Duration** means it is done. Make
   the finish duration longer than any quiet phase in a program (soaking,
   anti-crease pauses).
3. You get a notification with the run time and an **Emptied** button. Every
   reminder interval a reminder follows, up to the maximum.
4. Tapping **Emptied**, opening the door (optional door sensor) or starting
   the appliance again removes the notification from all phones.

Look at the power history of the appliance to pick the thresholds: the
standby power should be below Finish Power, and every phase of a program
above it or shorter than the finish duration.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Power Sensor | Power use of the appliance (W) | – |
| Appliance Name | Used in the notification | Washing machine |
| Notify Device(s) | Phones with the Companion app | – |
| Start Power / Duration | Running above this power for this long | 10 W / 2 min |
| Finish Power / Duration | Finished below this power for this long | 5 W / 5 min |
| Door Sensor | Optional; opening the door stops the reminders | none |
| Reminder Interval | Time between reminders | 30 min |
| Maximum Reminders | Reminders after the first notification (0 = none) | 3 |

---

## Smart Appliance Start

Starts an appliance that runs a program by itself (dishwasher, washing
machine, dryer) at the best moment before a deadline.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fsmart-appliance-start.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/smart-appliance-start.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

Load the appliance and make it ready. The **Ready Signal** turns on, for
example the remote start sensor of a Home Connect or Miele appliance, or an
`input_boolean` you switch on yourself. The appliance is then started once,
by the **Start Action**, at the first of these moments:

| Moment | When |
| --- | --- |
| Solar surplus | Exporting at least the required surplus for the surplus duration |
| Cheapest hours | Start of the cheapest stretch of Program Duration that finishes before the deadline |
| Deadline | The latest start to be finished by the deadline (also works without prices) |

The cheapest stretch is only planned once the prices up to the deadline are
known. Day-ahead prices are usually published around 13:00, so a dishwasher
loaded in the morning can still start on solar first. Without a price sensor
or grid sensor those options are skipped, so the blueprint can also be used
as a plain "finish by" timer.

The start action is yours to define: press the appliance's start button
(Home Connect, Miele), select and start a program, or turn on a smart plug for
an appliance that starts when it gets power. When the ready signal is an
`input_boolean`, turn it off in the start action. The optional notification
tells you why the appliance started and when it will be done.

**Price sensor format.** The upcoming prices are read from an attribute of the
price sensor that holds a list of entries with a start time and a price. Both
hourly and 15-minute prices work. Check **Developer Tools → States** for the
names your integration uses:

| Integration | Price Attribute(s) | Start Time Key | Price Key |
| --- | --- | --- | --- |
| Nord Pool (HACS) | `raw_today, raw_tomorrow` | `start` | `value` |
| ENTSO-e (HACS) | `prices` | `time` | `price` |
| Frank Energie (HACS) | `prices` | `from` | `price` |

Integrations that only offer prices through an action (the core Nord Pool,
Tibber and EnergyZero integrations) need a template sensor that stores the
result of that action in an attribute in this format.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Appliance Name | Used in the notification | Dishwasher |
| Ready Signal | On while the appliance is loaded and may be started | – |
| Start Action | Actions that start the appliance | – |
| Program Duration | How long the program takes | 180 min |
| Finished By | Deadline | 07:00 |
| Notify Device(s) | Optional phones to notify when started | none |
| Enable Switch | Optional `input_boolean`; the automation only acts while it is on | none |
| Grid Power Sensor | Optional net grid power (W or kW, negative = exporting) | none |
| Required Surplus | Export needed to start (0 = no solar starts) | 1500 W |
| Surplus Duration | How long the surplus must last | 10 min |
| Price Sensor | Optional dynamic tariff sensor | none |
| Price Attribute(s) / Start Time Key / Price Key | Where the upcoming prices are, see above | Nord Pool format |

---

## Smart Load Switch

Switches a flexible load (EV charger, electric or heat pump boiler,
dehumidifier, any smart plug) on and off based on solar surplus and energy
prices.

[![Open your Home Assistant instance and show the blueprint import dialog with this blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Ffromej%2Fhome-assistant-blueprint%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fsmart-load-switch.yaml)

```
https://github.com/fromej/home-assistant-blueprint/blob/main/blueprints/automation/smart-load-switch.yaml
```

Requires Home Assistant 2024.10 or newer.

### How it works

| Situation | Load |
| --- | --- |
| Not ready (e.g. car unplugged), or target reached | Off |
| One of the cheapest hours before the deadline | On |
| Price at or below the low price (default 0, i.e. free or negative) | On |
| Exporting at least the load power for the switch-on delay | On |
| Importing more than the allowed import for the switch-off delay | Off |
| A cheap hour just ended and importing more than the allowed import | Off |
| Anything else | Leave as is |

Without a grid sensor, the load only runs in the cheap hours and at low
prices.

**Cheapest hours** are picked once per period, from the previous deadline (or
the moment the ready signal turned on, whichever is later) to the next
deadline, and only once the prices up to the deadline are known. Hours that
run on solar do not count towards them. For an EV, set the ready signal to
"plugged in" and the target to your battery level sensor and charge limit.
For a boiler, set the target to the water temperature.

The load only gets on and off commands, so EV chargers charge at the current
they are set to. Use the optional enable switch (`input_boolean`) to pause the
automation and control the load by hand.

**Price sensor format.** The upcoming prices are read from an attribute of the
price sensor that holds a list of entries with a start time and a price. Both
hourly and 15-minute prices work. Check **Developer Tools → States** for the
names your integration uses:

| Integration | Price Attribute(s) | Start Time Key | Price Key |
| --- | --- | --- | --- |
| Nord Pool (HACS) | `raw_today, raw_tomorrow` | `start` | `value` |
| ENTSO-e (HACS) | `prices` | `time` | `price` |
| Frank Energie (HACS) | `prices` | `from` | `price` |

Integrations that only offer prices through an action (the core Nord Pool,
Tibber and EnergyZero integrations) need a template sensor that stores the
result of that action in an attribute in this format.

### Inputs

| Input | Description | Default |
| --- | --- | --- |
| Load | `switch` or `input_boolean` that turns the load on and off | – |
| Ready Signal | Optional; the load only runs while this is on | none |
| Target Sensor / Value | Optional; the load stops at or above this value | none / 80 |
| Enable Switch | Optional `input_boolean`; the automation only acts while it is on | none |
| Grid Power Sensor | Optional net grid power (W or kW, negative = exporting) | none |
| Load Power | Export needed to switch on (0 = no solar control) | 2000 W |
| Allowed Import | Switch off when importing more than this while on solar | 300 W |
| Switch-on / Switch-off Delay | How long the surplus / import must last | 5 / 5 min |
| Price Sensor | Optional dynamic tariff sensor | none |
| Price Attribute(s) / Start Time Key / Price Key | Where the upcoming prices are, see above | Nord Pool format |
| Cheapest Hours | Hours to run in the cheapest slots before the deadline (0 = off) | 4 h |
| Deadline | End of the period the cheapest hours are picked from | 07:00 |
| Run On Low Price / Low Price | Also run at or below this price | on / 0 |
