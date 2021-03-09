# Inline Filament Diameter Estimator, Lowcost (InFiDEL)

<p xmlns:dct="http://purl.org/dc/terms/" xmlns:vcard="http://www.w3.org/2001/vcard-rdf/3.0#">
  <a rel="license"
     href="http://creativecommons.org/publicdomain/zero/1.0/">
    <img src="https://licensebuttons.net/p/zero/1.0/80x15.png" style="border-style: none;" alt="CC0" />
  </a>
  Originally created by Thomas Sanladerer
</p>

*A cheap, yet precise filament diameter sensor, intended to compensate for filament diameter deviations in real-time.*

The InFiDEL is a cheap (< $5) filament diameter sensor intended for use with FDM 3d printers.
The sensor can be calibrated to provide surprisingly precise filament diameter readings in real-time. 
The main idea is to use the sensor to correct for filament diameter deviations while printing.

Based on this proof-of-concept: https://www.youmagine.com/designs/filament-diameter-sensor

This describes using the [Raspberry Pi Pico](https://www.raspberrypi.org/products/raspberry-pi-pico/) as the board.

## Assembly and Setup
[![Assembly and Setup Instructions on Youtube](https://img.youtube.com/vi/RYgdLPe_T0c/0.jpg)](https://www.youtube.com/watch?v=RYgdLPe_T0c)

For the Pi Pico, use a low-cost header from AliExpress.  Signal voltage is 3.3V.
Plug a +1.8 to +5V.V source directly into `VBUS`, and a ground into `GND`.

I2C `SDA`/`SCL` are `GP0` and `GP1`, respectively.

The Hall sensor's signal goes to `GP27`, `ADC1`; its ground goes to pin 33, `AGND`,
directly adjacent.  Pin 36 is 3.3V and Pin 39 provides whatever power you're using;
select an appropriate Hall sensor.

### Calibration
The Pi Pico can handle calibration in several ways.  Each requires you to use a
number of calibration samples less than 1mm and round; a caliper is highly
recommended here.

Thonny can run Python directly on the board.  Plug the Pico into USB and use the
included program to give a continuous sensor readout.  Record the sensor values
for each diameter and enter them into `infidel.calibration.json` in the format:

```json
{
  'calibration':
  [
    {
     'reading': <sensor reading>,
     'diameter': <sample diameter>
    },
    ...
  ]
}
```

Multiple samples of a given diameter are allowed. `numpy` computes a quadratic
regression whenever calibration data is loaded (at power on) or added.

Alternatively, use a jumper wire to connect `GP15` and `GP14` (pins 20 and 19),
and the jump to pin 18 (GND) any of pin 17 (1.5mm), 16 (1.7mm), or 15 (2.0mm).
Use a drillbit of each precise diameter to give a sample.  When each pin is
jumped, it deletes all samples for that diameter; it then blinks each 1 second,
taking a sample for that diameter.

The last method is bidirectional communication over I2C, but this requires
host support.  The printer firmware may not support this.  Appropriate commands
add, delete, retrieve, or modify sample data.


## BOM

### Printed Parts
- 1 Block
- 1 Lever

#### Note:
Parts should preferably be printed in PETG, ABS or ASA as PLA may creep significantly over time.

### Electronics
 - 1 Raspberry Pi Pico ($4)
 - 1 SS495A linear hall effect sensor (or comparable)

### Fasteners
- 2 M3x8 screws (eg ISO 4762 M3x8)
- 1 3x24mm pin (eg DIN 7 3x24mm, for mating lever to the block)
- 1 3x16mm pin (eg DIN 7 3x16mm, for roller bearing in lever)
- 1 3x18mm pin (eg DIN 7 3x18mm, for idler bearing in block)
- 6 M3 threaded inserts
- 1 M5x5 or M5x8 grub screw (eg ISO 4026 M5x5, for pre loading spring) 

Pins may be replaced with screws or grubscrews, which may result in decreased precision.

### Other Hardware
- 1 6x2mm magnet (eg N35)
- 4 623 bearings (preferably 623-2Z / 623-ZZ)
- 1 Ballpoint pen spring (or other soft 4mm spring)
- Short length of PTFE tube

### Calibration Accessories
- 1 1.5mm drill bit
- 1 1.7mm or 1.8mm drill bit 
- 1 2mm drill bit
- Calipers for verifying drill bit shaft diameter

Drill bits may be replaced with similar diameters, so long as diameters are
corrected in the calibration file.
