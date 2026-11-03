# Calibration

The HX711 returns a raw number without unit. The weight is

```
weight = (raw - tare - drift) * scale
```

- `tare` is the raw value of the empty bed, set by the **Tare** button.
- `scale` is in kilograms per raw count, set by the **Calibrate** button.
- `drift` is a small correction applied by the auto-zero while the bed is empty.

Tare and scale are stored on the device and survive reboots and power cuts.

## Steps

1. Make the bed as usual (duvet, pillows) and leave it empty.
2. Press **Tare**. **Weight** stays unknown for now: it is not calibrated yet.
3. Put a known weight on the bed, as close as possible to where you sleep. A person whose weight you
   know is the easiest: someone of 70 kg lying in their usual place. A pack of water bottles works too,
   but at least 10 kg, the more the better.
4. Set **Calibration weight** to that weight.
5. Press **Calibrate**. **Weight** now shows about that weight.
6. Remove the weight: **Weight** comes back near 0.

If **Calibrate** does nothing, the log says why: the reading did not change enough since the tare
(at least 1000 raw counts). Press **Tare** first, then add the weight.

## Threshold

With one cell under one leg, a person of 70 kg may read 25 kg on one side of the bed and 40 kg on the
other. Lie in bed in your usual positions, look at **Weight**, and set **Occupied threshold** below
the lowest value you see, but well above what a cat or a pile of clothes weighs.

For two people, the sensor tells whether the bed is occupied, not by whom. To detect "both in bed",
create a template binary sensor in Home Assistant on the **Weight** value with a second threshold.

## When to recalibrate

- After moving the cell or the bed.
- After changing the mattress.
- If **Weight** is far from 0 with an empty bed: press **Tare** (the auto-zero only corrects drift
  smaller than 2 kg).
