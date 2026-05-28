<div align="center">

# ESP32 Fan Controller

**A small KiCad board to keep my home server cabinet cool. 4 PWM fans, 4 temperature probes, one ESP32.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Status: PCB ordered](https://img.shields.io/badge/status-PCB%20%26%20parts%20ordered-blue.svg)](#roadmap)
[![KiCad: 10](https://img.shields.io/badge/KiCad-10-blueviolet.svg)](https://www.kicad.org/)
[![Schematic: ERC clean](https://img.shields.io/badge/schematic-ERC%20clean-2ea44f.svg)](#)
[![PCB: DRC clean](https://img.shields.io/badge/PCB-DRC%20clean-2ea44f.svg)](#)

<a href="docs/images/pcb-3d-render.png"><img src="docs/images/pcb-3d-render.png" alt="ESP32 Fan Controller, KiCad 3D render" width="60%"></a>

</div>

## Why this exists

My home servers live in a small cabinet, hidden inside a piece of furniture I built around them. It is a credenza with a stone top, a perforated metal grille set into that top so the warm air can escape upward, and cane mesh side panels so fresh air can come in from below.

The room itself has air conditioning, but the cabinet does not. Without something actively pushing the warm air out, it just stacks up under the stone. So I needed a way to manage the airflow.

<table align="center">
  <tr>
    <td align="center">
      <a href="docs/images/server-cabinet.jpg"><img src="docs/images/server-cabinet.jpg" alt="The server cabinet, with the perforated stone-top grille and cane-mesh panels, click for full size" width="420"></a>
    </td>
  </tr>
  <tr>
    <td align="center"><sub>The cabinet. Stone top with the perforated exhaust grille against the wall, servers behind the cane mesh panels.</sub></td>
  </tr>
</table>

I started with two breadboard prototypes (see [How I got here](#how-i-got-here)). The first one was just about understanding the temperatures: where to put the probes, where it actually gets hot. The second one was for the fans, to see how they really move the air through that perforated stone grille.

Once both questions were answered, I designed this PCB. It is a small two layer board, all through-hole so I can solder it by hand on a Sunday afternoon, around €14 in parts. It runs [ESPHome](https://esphome.io/), which means it shows up directly in Home Assistant without writing any firmware.

> Heads up: the PCB and the parts are ordered, but nothing is assembled yet. The ESPHome config will follow once the board is here.

## Contents

- [Features](#features)
- [Hardware overview](#hardware-overview)
  - [ESP32 GPIO map](#esp32-gpio-map)
- [How I got here](#how-i-got-here)
- [Build one yourself](#build-one-yourself)
- [Repository layout](#repository-layout)
- [Roadmap](#roadmap)
- [License](#license)
- [Thanks](#thanks)

## Features

- 4 independent PWM fan channels (designed around Noctua NF-A14 PWM, but any standard 4-pin PC fan works)
- 4 tachometer (RPM) inputs, open-collector, with on-board pull-ups to 3.3 V
- 4 DS18B20 1-Wire temperature probes on a shared bus (single 4.7 kΩ pull-up)
- Single 12 V external input. An on-board MP1584EN buck module generates the 5 V for the ESP32
- Per-fan PTC fuse and a TVS diode at the 12 V input, for protection
- I²C expansion header (3.3 V / GND / SDA / SCL)
- Two indicator LEDs: a green one for power (lit when the 5 V rail is up) and a yellow one for status (driven by GPIO 2)
- A small tactile button for boot / user input, shared with GPIO 0
- Everything through-hole, so it can be hand-soldered without a reflow oven
- Bare board, 155 × 85 mm, with M3 mounting holes

## Hardware overview

| Block      | Component                                                       |
|------------|-----------------------------------------------------------------|
| MCU        | ESP32 DevKitC V4 (AZDelivery, 100 % Espressif layout)           |
| Power      | External 12 V / 2 A PSU, then on-board MP1584EN buck to 5 V     |
| Fans       | 4 Noctua NF-A14 PWM, plugged into 4-pin headers                 |
| Sensors    | 4 DS18B20 waterproof probes on screw terminals                  |
| Protection | 1.5KE15A TVS on the 12 V input, 4 MF-R050 PTC fuses on the fans |
| Enclosure  | No enclosure for v1, just M3 mounting holes on the bare board   |

PCB is two-layer, 155 × 85 mm, 1.6 mm thick.

<table align="center">
  <tr>
    <td align="center" width="50%">
      <a href="docs/images/pcb-layout.png"><img src="docs/images/pcb-layout.png" alt="PCB top-down layout, click for full size" width="300"></a>
    </td>
    <td align="center" width="50%">
      <a href="docs/images/pcb-3d-render.png"><img src="docs/images/pcb-3d-render.png" alt="KiCad 3D render, click for full size" width="300"></a>
    </td>
  </tr>
  <tr>
    <td align="center"><sub>2D layout. F.Cu in red, B.Cu in blue.</sub></td>
    <td align="center"><sub>KiCad 3D render of the assembled board.</sub></td>
  </tr>
</table>

### ESP32 GPIO map

| Pin            | Use                                  |
|----------------|--------------------------------------|
| 25, 26, 32, 33 | PWM out for fans 1 to 4              |
| 34, 35, 36, 39 | Tach in for fans 1 to 4 (input-only) |
| 23             | 1-Wire bus for the DS18B20 probes    |
| 21 / 22        | I²C SDA / SCL                        |
| 2              | Status LED (D3 yellow, R6 470 Ω)     |
| 0              | Boot / user button (SW1)             |

## How I got here

Before committing to a PCB, I validated the design with two breadboard stages.

### Stage 1, temperature monitoring with Home Assistant

I started with an ESP32 **LoLin** board and **two DS18B20 probes**, flashed with [ESPHome](https://esphome.io/) and added to Home Assistant. The question for this stage was simple: what does the temperature actually do in the server cabinet? It gave me the baseline graphs that justified active cooling, and it told me where the probes should sit (intake, exhaust, ambient).

The ESPHome YAML I used is kept here for reference: [`docs/stage1-lolin32-esphome.yaml`](docs/stage1-lolin32-esphome.yaml). Shared 1-Wire bus on GPIO 4, two `dallas_temp` entries by 64-bit address.

<table align="center">
  <tr>
    <td align="center" width="50%">
      <a href="docs/images/proto-lolint-esp.jpg"><img src="docs/images/proto-lolint-esp.jpg" alt="ESP32 LoLin with 2 DS18B20 probes on a protoboard, click for full size" width="300"></a>
    </td>
    <td align="center" width="50%">
      <a href="docs/images/homeassistant-esphome.png"><img src="docs/images/homeassistant-esphome.png" alt="Home Assistant device page for the LoLin32, click for full size" width="300"></a>
    </td>
  </tr>
  <tr>
    <td align="center"><sub>ESP32 LoLin with two DS18B20 probes.</sub></td>
    <td align="center"><sub>Home Assistant device page, both probes live (28.7 °C and 29.8 °C).</sub></td>
  </tr>
</table>

### Stage 2, the fan bench

For the second stage, I drove a fan directly from a separate breadboard, to actually measure how the airflow influences the temperature. Fan position, speed versus Δtemperature, how fast the cabinet responds. This is where I confirmed that the Noctua NF-A14 was the right fan, validated the PWM strategy, and figured out the per-fan power budget.

<table align="center">
  <tr>
    <td align="center">
      <a href="docs/images/proto-fan-bench.jpg"><img src="docs/images/proto-fan-bench.jpg" alt="Fan power and airflow test on a 30 by 70 mm protoboard, click for full size" width="400"></a>
    </td>
  </tr>
  <tr>
    <td align="center"><sub>Fan power and airflow test on a 30 × 70 mm protoboard.</sub></td>
  </tr>
</table>

The PCB in this repository is the consolidation of both stages: 4 probes (intake, exhaust, two ambient) and 4 PWM-controlled fans on a single board instead of breadboards.

## Build one yourself

1. **Order the bare PCB.** Any fab works, I used [Aisler](https://aisler.net/). You can upload `kicad/esp32-fan-controller.kicad_pro` directly, or export gerbers first if your fab needs them.
2. **Order the parts**, around €14 excluding VAT and shipping. The quick-paste block in [`bom/ORDER.md`](bom/ORDER.md) is verified against live Farnell France stock. Paste it into Farnell's Quick Paste / Multi-Add tool. The full BOM with notes is in [`BOM.md`](BOM.md).
3. **Pre-set the MP1584EN buck module to 5.00 V before soldering it in.** Apply 12 V on its input with a bench supply, and turn the trimpot until the output reads 5.00 V.
4. **Hand-solder.** All through-hole. The order I would suggest: passives, then diodes and LEDs, then sockets and headers, then screw terminals, and finally the ESP32 socket.
5. **Power-up check.** 12 V on J1, confirm 5 V at the M1 output pin, then plug in the ESP32.
6. **Flash [ESPHome](https://esphome.io/)**, no custom firmware needed. The v1 config will land in `docs/` once the board is assembled. In the meantime, the Stage 1 prototype config at [`docs/stage1-lolin32-esphome.yaml`](docs/stage1-lolin32-esphome.yaml) is a good starting point.

## Repository layout

```
.
├── kicad/                            # KiCad 10 project (schematic and PCB)
├── bom/                              # BOM CSV and ordering sheets
│   └── orders/                       # Generated distributor cart CSVs
├── docs/
│   ├── images/                       # Photos, schematic renders, screenshots
│   └── stage1-lolin32-esphome.yaml   # ESPHome config used on the temperature prototype
├── BOM.md                            # Human-readable BOM with notes
├── LICENSE                           # MIT
└── README.md
```

## Roadmap

**Next**

- [ ] Assemble the v1 board and bring it up
- [ ] Write the [ESPHome](https://esphome.io/) config for the board (4 PWM fans with tach feedback, shared 1-Wire DS18B20 bus) and add it to Home Assistant, same approach as Stage 1

**Later**

- [ ] An actual enclosure for the PCB. The CamdenBoss CNMB/9 DIN-rail kit is a candidate, the board is already short-edge-connector friendly
- [ ] A v2 silkscreen polish, based on what I learn during assembly

**Done**

- ✅ Schematic, ERC clean
- ✅ PCB layout, DRC clean, 0 violations, 0 unconnected
- ✅ Bare PCB ordered (Aisler)
- ✅ BOM verified against live Farnell stock
- ✅ Components ordered (Farnell)

## License

MIT, see [LICENSE](LICENSE). Hardware design files are released under the same terms, so feel free to fork, modify, fabricate and share.

## Thanks

- The KiCad team for the EDA suite
- Espressif for the ESP32 and the DevKitC reference design
- AZDelivery for an affordable ESP32 DevKitC V4 clone
- [ESPHome](https://esphome.io/), which turned the Stage 1 prototype into a one evening job
- [openotters](https://github.com/openotters/openotters), which I used as a template for this README
