# Design Overview

## 1. Requirements

| # | Requirement | Implementation |
|---|---|---|
| R1 | Operate from a single 20–25 V DC supply | DC jack `J1` feeding two buck converters |
| R2 | Drive a 12 V DC fan | 12 V buck (`U2`), low-side MOSFET `Q1`, fan terminal `J5` |
| R3 | Measure temperature | TMP36 (`U5`) read by MCU ADC on `PA4` |
| R4 | Control fan speed automatically | PWM on `PA6` from the STM32F411 |
| R5 | In-circuit programming and debug | SWD header `J3` |

## 2. Power Tree

```text
J1 (20–25 V)
 ├── U2 TPS563300  ──► 12 V ──► Fan (J5)
 └── U1 TPS54202   ──► 5 V  ──► U3 AMS1117-3.3 ──► 3.3 V ──► MCU, TMP36, LEDs
```

### 12 V rail (U2)

- Inductor `L1` 12 µH; output capacitor `C4` 47 µF
- Feedback divider `R1` = 140 kΩ (top), `R3` = 10 kΩ (bottom); ratio 15:1, implying a 0.8 V feedback reference for 12 V out
- Input capacitors `C6` 10 µF / 50 V and `C7` 100 nF; bootstrap `C2` 100 nF

### 5 V rail (U1)

- Inductor `L2` 15 µH; output capacitor `C23` 47 µF; feed-forward `C3` 68 pF
- Feedback divider `R2` = 100 kΩ, `R4` = 13.7 kΩ:
  `Vout = 0.596 V × (1 + 100 / 13.7) ≈ 4.95 V` (typical reference; confirm in the datasheet)
- Input capacitor `C5` 6.8 µF / 35 V; bootstrap `C1` 100 nF

### 3.3 V rail (U3)

- AMS1117-3.3 fed from the 5 V rail; `C9` (input) and `C8` (output) 10 µF
- Dissipation at full load: `(5 V − 3.3 V) × I`. At 100 mA this is about 0.17 W, which is comfortable for SOT-223.

## 3. Sensing Chain

```text
3.3 V ─► TMP36 (U5) ── Vout ── R13 1 kΩ ──┬── PA4 (ADC)
          │ C21 0.1 µF bypass            └── C22 100 nF ─ GND
          SHUTDOWN tied to 3.3 V
```

`T(°C) = (Vout − 0.5 V) / 0.010 V/°C`. The RC filter has a corner frequency of about 1.6 kHz (`1 / (2π · 1 kΩ · 100 nF)`), which removes ADC-sampling kickback and high-frequency noise while remaining fast compared to thermal dynamics.

## 4. Fan Driver

- `Q1` AO3400A (30 V, 5.8 A, logic-level) switches the fan's return path to ground
- `R14` 150 Ω limits gate-charge current from `PA6`
- `R15` 10 kΩ holds the gate low while the MCU pin is high-impedance (reset/boot), keeping the fan off
- `D4` SS14 clamps inductive flyback when the fan is turned off

## 5. Microcontroller Support Circuitry

| Function | Parts |
|---|---|
| Reset | `S2` push button, `R6` 10 kΩ pull-up, `C17` 0.1 µF |
| Supply decoupling | `C12`–`C14` 0.1 µF on 3.3 V |
| Analog reference | `L3` ferrite bead (150 Ω @ 100 MHz), `C15`/`C16` 0.1 µF on `VREF+` |
| Core regulator | `C18` 1 µF on `VCAP_1` |
| 32.768 kHz clock | `Y2` active oscillator, `C20` 8 pF, `R12` 10 kΩ (tri-state pull-up), output to `PC14` |
| Debug | `J3` 4-pin SWD: SWDIO (`PA13`), SWCLK (`PA14`), 3.3 V, GND |
| Indicators | `D2` blue LED + `R9` 1.5 kΩ on `PC13`; `D3` red LED + `R10` 5.1 kΩ (power) |

## 6. Mechanical / Layout Notes

- Irregular board outline, with the DC jack on the upper left, the fan terminal at the lower left and the SWD header at the lower right
- Power stages (inductors, diode, buck ICs) are grouped on the left half; the MCU and its decoupling are on the lower right
- Schematic sheets: Main System (A4), Power (A4), MCU (A3), Sensor (A4), Actuator (A4)
