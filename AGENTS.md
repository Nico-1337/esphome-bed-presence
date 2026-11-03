# AGENTS.md

Instructions for AI coding assistants (Claude Code, Codex, Copilot, Cursor…) working on this
repository, or on a Home Assistant / ESPHome setup that uses it.

## Layout

- `esphome/bed-presence.yaml`: the device. Everything a user changes is in `substitutions`.
- `esphome/secrets.example.yaml`: the secrets the device needs. Never commit a real `secrets.yaml`.
- `blueprints/automation/bed_alarm.yaml`: the alarm.
- `docs/`: wiring, calibration, troubleshooting. `README.md` (English) and `README.fr.md` (French)
  must say the same thing.

## Rules for the device

- The weight formula is `(raw - tare - drift) * scale`. `tare` and `scale` are restored globals, set
  only by the buttons. `drift` is not restored, on purpose: the auto-zero runs every few minutes and
  writing it to flash would wear the ESP8266 flash.
- Never tare automatically at boot. Someone may be in bed after a power cut.
- `scale` 0 means "not calibrated": the weight must stay `NAN`, and the occupancy unknown.
- Keep `restore_from_flash: true` on ESP8266.
- No secret, password or real Wi-Fi name in the YAML: `!secret` only.
- Validate with `esphome config esphome/bed-presence.yaml` (with a `secrets.yaml` made from the example)
  before proposing a change.

## Rules for the blueprint

- It must stop in every case: bed empty for `out_of_bed_for`, or `max_minutes` reached.
- It only starts if the bed is occupied at the alarm time.
- Do not add a snooze: getting out of bed is the point.
- Inputs that run actions use the `choose: [] / default: !input ...` pattern.

## Using the sensor in automations

- Use the **Occupied** binary sensor, not the weight: it already has delays against turning over.
- The sensor says the bed is occupied, not by whom.
- Ask the user before any test that rings, speaks or wakes people up.

## Writing

- Short sentences. Do not claim a feature was tested on hardware unless the user says it was.
