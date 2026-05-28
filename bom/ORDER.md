# Order Sheet — ESP32 Fan Controller

## Farnell France (1 board, no spares)

Paste this into Farnell **Quick Paste / Multi-Add** tool on [fr.farnell.com](https://fr.farnell.com/):

```
2507750,5
2579016,1
9451188,2
1907193,1
2765092,1
1142509,1
1612439,1
9350420,4
3041529,1
2356155,6
3041530,4
2329626,1
2329609,4
2329680,1
2329685,3
176432,1
```

**Total: ~£11.65 (~€14 excl. VAT/shipping, ~€22 all-in)**

> **Verified 2026-05-27**: all SKUs above are STOCKED on Farnell UK with adequate inventory. Four parts were swapped from the initial pick — the originals were either NO_LONGER_STOCKED, out-of-stock, or DIRECT_SHIP (longer lead time).

### Detailed Farnell BOM

| Farnell SKU | Qty | MPN | Description | £/ea | £Total |
|-------------|----:|-----|-------------|-----:|-------:|
| 2507750 | 5 | K104K15X7RF5UH5 | 100nF 50V X7R radial (C2-C5, C7) | 0.08 | 0.41 |
| 2579016 | 1 | TAP106K016SCS | 10µF 16V tantalum (C6) | 0.96 | 0.96 |
| 9451188 | 2 | MCGPR25V107M6.3X11 | 100µF 25V electrolytic (C1, C8) — Multicomp Pro | 0.15 | 0.30 |
| 1907193 | 1 | ECA1CHG100 | 10µF 16V electrolytic (C9) | 0.21 | 0.21 |
| 2765092 | 1 | 1.5KE15A | TVS diode DO-201 (D1) — Littelfuse | 0.30 | 0.30 |
| 1142509 | 1 | L-934LGD | 3mm green LED (D2) — Kingbright | 0.09 | 0.09 |
| 1612439 | 1 | TLLY4400 | 3mm yellow LED (D3) — Vishay | 0.31 | 0.31 |
| 9350420 | 4 | MF-R050 | PTC 0.5A radial (F1-F4) | 0.34 | 1.37 |
| 3041529 | 1 | 1715721 | Phoenix MKDS 2-pin (J1) | 1.20 | 1.20 |
| 2356155 | 6 | 61300411121 | Pin Header 1×4 2.54mm (J3-J7, M1) | 0.08 | 0.50 |
| 3041530 | 4 | 1715734 | Phoenix MKDS 3-pin (J8-J11) | 1.52 | 6.08 |
| 2329626 | 1 | CFR25J1K0 | 1kΩ axial 5% (R1) | 0.04 | 0.04 |
| 2329609 | 4 | CFR25J10K | 10kΩ axial 5% (R2-R5) | 0.03 | 0.11 |
| 2329680 | 1 | CFR25J470R | 470Ω axial 5% (R6) | 0.04 | 0.04 |
| 2329685 | 3 | CFR25J4K7 | 4.7kΩ axial 5% (R7-R9) | 0.04 | 0.11 |
| 176432  | 1 | B3F-1000 | Tactile 6mm THT (SW1) | 0.12 | 0.12 |

⚠️ **MOQ tip:** Some lines (R series, LEDs, B3F-1000) have MOQ of 10 — Farnell may auto-bump qty. Worth keeping spares anyway for hand-soldering.

## Other Sources

| Item | Qty | Status | Source / Price |
|------|----:|--------|----------------|
| ESP32 DevKitC V4 (AZDelivery, 100% Espressif layout) | 1 | **to order** | [az-delivery.de](https://www.az-delivery.de/en/products/esp-32-dev-kit-c-v4) — €13.99 |
| 12V/2A PSU (to be chosen) | 1 | **to source** | Amazon FR brick, or Mean Well brick at Farnell separately |
| MP1584EN buck module | 1 | ✓ owned | — |
| Noctua NF-A14 PWM 120mm | 4 | ✓ owned | — |
| DS18B20 waterproof probe | 4 | ✓ owned | — |

**Estimated total (excl. owned items, excl. PCB, excl. enclosure, excl. PSU):** ~€30–40 (Farnell €15–20 + ESP32 €14 + shipping)

## Notes

- **Spares**: for hand-soldering, consider ordering +25% extra on small passives (LEDs, resistors). Farnell's MOQ of 10 on most items already gives you spares.
- **PCB**: ordered separately from Aisler (~€30 for 3 boards)
- **Hot glue**: for fan plug strain relief — buy locally
- **M3 screws + standoffs**: if using standalone (CNMB/9 uses side slots, no holes needed)
