# Bed presence for Home Assistant

**English** | [Français](README.fr.md)

A load cell under the bed, an HX711 and an ESP8266 running ESPHome. Home Assistant knows whether
someone is in bed. The repository comes with a blueprint for an alarm that keeps ringing until you are
actually out of bed.

> **Status:** this version has not been tested on hardware yet. It is a rewrite of a sensor I ran in
> 2020 (see [Background](#background)). The firmware compiles for a NodeMCU (ESPHome 2026.10); please open an
> issue if something behaves differently on your board.

## What you get

ESPHome device (`esphome/bed-presence.yaml`):

| Entity | Type | What it does |
|---|---|---|
| Occupied | binary sensor | on while the weight is above the threshold, with delays so turning over does not flip it |
| Weight | sensor (kg) | calibrated weight, unknown until you calibrate |
| Raw value | sensor (diagnostic) | HX711 reading, for calibration and debugging |
| Occupied threshold | number (kg) | adjustable from Home Assistant, default 20 kg |
| Calibration weight | number (kg) | the known weight you calibrate with |
| Tare / Calibrate / Restart | buttons | calibration without reflashing |

- Tare and calibration are set from Home Assistant and survive reboots and power cuts.
- Slow auto-zero: load cells drift with temperature. While the bed is empty and the reading is within
  2 kg of zero, the zero follows the drift. This correction is never written to flash.
- No tare at boot: after a power cut at night, someone may be in bed.

Blueprint (`blueprints/automation/bed_alarm.yaml`): at the alarm time, if the bed is occupied, it runs
your ring action every minute until the bed has stayed empty for a minute. Getting back into bed
starts it again. It gives up after 30 minutes. All of these are settings.

[![Import the blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FNico-1337%2Fesphome-bed-presence%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fbed_alarm.yaml)

## Hardware

- An ESP8266 board (NodeMCU or Wemos D1 mini). An ESP32 works too: change the `esp8266:` block and
  the pins.
- An HX711 module.
- Load cells: one bar load cell under one bed leg, or four half-bridge cells (the ones from bathroom
  scales) under the four legs. See [docs/wiring.md](docs/wiring.md).
- A USB power supply.

This is a presence sensor, not a scale. With one cell under one leg, the reading depends on where you
lie. That is fine: it only needs to tell an empty bed from an occupied one.

## Install

1. Wire it: [docs/wiring.md](docs/wiring.md).
2. Copy `esphome/bed-presence.yaml` to your ESPHome folder, create `secrets.yaml` from
   `secrets.example.yaml`, and adjust the `substitutions` (pins, name).
3. Flash it, then add the device in Home Assistant (it is discovered).
4. Calibrate it: [docs/calibration.md](docs/calibration.md). Two buttons and a known weight.
5. Set **Occupied threshold**: about half the lightest sleeper's weight, as measured by **Weight**.
6. Import the blueprint and create an alarm.

Problems: [docs/troubleshooting.md](docs/troubleshooting.md).

## Background

In 2020 I slid a load cell under my bed, with an HX711 and an ESP8266 running ESPHome, and an
alarm that only stopped when the bed was empty. The original configuration was about 35 lines: one
HX711 sensor, calibrated in two points directly in the YAML. This repository is a new, generic
version: calibration and threshold from Home Assistant, presence with delays, drift correction, and
the alarm as a blueprint. It was written with AI assistance and reviewed by me.

There is also an [AGENTS.md](AGENTS.md) for AI coding assistants working on it.

## License

MIT. Made by Nicolas Holder. More projects on [nholder.fr](https://nholder.fr/en/).
