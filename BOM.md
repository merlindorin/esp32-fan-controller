# Bill of Materials — ESP32 Fan Controller

Server airflow controller: 4× PWM fans driven by ESP32, regulated from 4× DS18B20 temperature probes.

## Main components

| Qty | Part | Notes |
|----:|------|-------|
| 1 | ESP32 DevKitC V4 | MCU board, mounted on PCB via female headers |
| 1 | 12V/2A PSU | External — DIN-rail (e.g. Mean Well HDR-30-12) or desktop brick |
| 1 | MP1584EN buck module (pre-built, adjustable) | 12V→5V step-down for ESP32. Pre-set trimpot to **5.00 V** before installing |
| 4 | Noctua NF-A14 PWM | 12V, 0.13A each (0.52A total on 12V rail) |
| 4 | DS18B20 waterproof probe | 1-Wire digital temp sensor, shared bus |
| 4 | Phoenix MKDS 1,5/3-5,08 (3-pin screw terminal block, 5.08 mm pitch) | For DS18B20 probes — wires enter through CNMB/9 perforated terminal guards. Pin 1=GND, Pin 2=DATA, Pin 3=+3V3. Same family as J1. |
| 4 | 4-pin PC fan header (2.54mm pitch) | **NOT** Molex KK 254 — keying differs, Noctua plugs won't mate |
| 1 | 2-pin terminal block (5.08mm pitch) | 12V input |
| 1 | 1×4 pin header, 2.54 mm pitch | Socket for MP1584EN module (M1) |
| 2 | Female header 1×19, 2.54mm pitch | ESP32 DevKitC V4 socket |

## Passives

| Qty | Part | Purpose |
|----:|------|---------|
| 1 | 4.7 kΩ resistor (0603 or THT) | 1-Wire bus pull-up to 3.3V |
| 4 | 10 kΩ resistor (0603 or THT) | Tach signal pull-up to 3.3V |
| 1 | 100 µF electrolytic, 25V | 12V rail bulk decoupling (C1, near J2) |
| 1 | 100 µF electrolytic, 25V | Buck input bulk (C8, at M1 +12V pin) |
| 1 | 100 µF electrolytic, 25V | Buck output decoupling (C9, at M1 +5V pin) |
| 1 | 10 µF ceramic, 10V | 3.3V rail decoupling |
| 4 | 100 nF ceramic, 50V | Per-fan VCC decoupling |
| 1 | 10 µF ceramic, 16V | 5V rail decoupling near ESP32 socket (C6) |

## Protection & debug

| Qty | Part | Purpose |
|----:|------|---------|
| 4 | PTC fuse, 0.5A hold / 1A trip (e.g. Bourns MF-R050) | Per-fan 12V protection |
| 1 | TVS diode, 1.5KE15A (axial DO-15 THT) | 12V input transient protection |
| 1 | LED (green, 3mm) + 1 kΩ resistor | Power indicator |
| 1 | LED (blue/yellow, 3mm) + 1 kΩ resistor | Status, driven by ESP32 GPIO |
| 1 | Tactile pushbutton | Optional reset/user input |
| 1 | Pin header 1×4, 2.54mm pitch | I²C expansion (3.3V / GND / SDA / SCL) |

## Mechanical

| Qty | Part | Notes |
|----:|------|-------|
| 1 | PCB | ~155 × 85 mm, 1.6 mm thick. Slides into CNMB/9 side slots; 4× M3 mounting holes (H1–H4) also present for standalone use |
| 4 | M3 standoff + screws, optional | For standalone PCB testing/mounting outside CNMB/9 |
| 1 | CamdenBoss CNMB/9/KIT | DIN rail enclosure, 9M (159 × 90 × 58 mm). Farnell `3954255`. Includes body + grey cover + 6× perforated terminal guards |

## Power budget

| Rail | Source | Consumers | Total draw | Margin |
|------|--------|-----------|-----------:|-------:|
| 12V | External 12V/2A | 4× NF-A14 @ 0.13A + buck input (~0.3A) | ~0.85A | ~1.15A |
| 5V | MP1584EN buck (3A) | ESP32 DevKitC V4 (~0.5A peak with WiFi) | ~0.5A | ~2.5A |
| 3.3V | ESP32 onboard LDO | DS18B20 ×4, pull-ups, LED | ~0.05A | comfortable |

## Design decisions (locked)

- **DS18B20 power mode:** standard 3-wire (VCC + DATA + GND). Cleaner, less noise-sensitive.
- **Expansion:** 1× I²C header (3.3V / GND / SDA / SCL) on free GPIO. No UART header (USB serial covers it).
- **Enclosure:** CamdenBoss CNMB/9/KIT (9M DIN rail, 159 × 90 × 58 mm). PCB slides into bottom side slots (Position A), max **86.5 × 156 mm**. Target PCB **155 × 85 mm**. No mounting holes — side slots only. Connectors exit the **short edges** (85 mm sides), through end-face terminal guards.
- **Power architecture:** single external 12V/2A PSU feeds the board through one 12V terminal block. On-board MP1584EN buck module generates 5V for the ESP32. No mains on the board.
