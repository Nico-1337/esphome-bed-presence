# Troubleshooting

## Raw value is unknown or stuck

- Check DT and SCK against `dout_pin` and `clk_pin`.
- VCC on 3V3, GND shared with the ESP8266.
- Try another HX711 module: cheap ones are sometimes faulty.

## Raw value jumps around

- Noise of a few hundred counts is normal: the median filter smooths it.
- Large jumps: a loose wire, or a long cable between the cells and the HX711.
- The leg touches the floor next to the cell, or the cell is on carpet. It needs a hard, flat support.

## Weight is unknown

The device is not calibrated: follow [calibration.md](calibration.md). The weight stays unknown as
long as `scale` is 0.

## Weight goes negative when someone lies down

Not a problem after calibration: the scale is negative and the weight comes out positive. If it is still
negative after **Calibrate**, the known weight was removed before pressing the button.

## Occupied flickers

- Raise `delayed_off` (default 20 s) in the substitutions.
- Lower **Occupied threshold**: on a single cell, some positions put little weight on that leg.

## Weight drifts over days

Normal for load cells, especially with temperature. The auto-zero corrects drift under 2 kg while the
bed is empty, every 10 minutes. A larger offset needs **Tare**. The auto-zero is not applied at boot on
purpose: after a power cut, someone may be in bed.

## Tare or calibration lost after a power cut

`restore_from_flash: true` must be in the `esp8266:` block. Without it, the ESP8266 keeps them only
until power is lost.

## The alarm does not ring

- The blueprint only starts if the bed is occupied at the alarm time, and only on the selected days.
- Look at the automation trace: the condition that failed is shown.
- If the bed sensor is unavailable, the alarm does not ring, and if it becomes unavailable while
  ringing, it stays quiet until the time limit.

## The alarm keeps ringing after I got up

The bed must stay empty for **Out of bed for** (default 60 s). Sitting on the edge of the bed may
still count as occupied: raise **Occupied threshold** a little.
