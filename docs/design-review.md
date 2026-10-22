# Design Review Notes

Items found while reviewing the schematic, BOM and PCB layout. Severity: **High** = verify before fabrication, **Med** = recommended, **Low** = polish.

| # | Sev. | Observation | Recommendation |
|---|---|---|---|
| 1 | High | `C18` (1 µF) is on `VCAP_1` of the STM32F411 | Confirm the value required by the STM32F411 datasheet for `VCAP_1` and correct it if it is not 1 µF |
| 2 | High | `BOOT0` is not visibly tied to a defined level on the MCU sheet | Add a pull-down (for example 10 kΩ) to GND, or a jumper, so the MCU boots from Flash reliably |
| 3 | High | No reverse-polarity or surge protection on the 20–25 V input | Add a series Schottky/P-MOSFET and a TVS diode at `J1` |
| 4 | Med | `C4` and `C23` are 47 µF / 16 V X5R; DC-bias derating reduces ceramic capacitance a lot. `C8`/`C9` (10 µF / 25 V) are already noted in the BOM as derating to about 3.5 µF | Use higher-voltage-rating or larger-case parts, and check regulator stability and output ripple against the datasheets |
| 5 | Med | Fan current is limited by `D4` (SS14, 1 A) and the 12 V rail (3 A) | State the maximum supported fan current (≤ 1 A) in the documentation, or upsize `D4` |
| 6 | Med | `U5` (TMP36) sits close to the 12 V inductor/diode area, so self-heating can bias the reading | Move the sensor away from power components, or onto a thermally isolated spur |
| 7 | Med | LED currents are very low: blue `D2` (Vf ≈ 3.2 V) through 1.5 kΩ from 3.3 V gets ≈ 0.07 mA; red `D3` through 5.1 kΩ gets ≈ 0.28 mA | Lower `R9`/`R10` (for example 330 Ω for red; use the 5 V rail or a lower-Vf LED for blue) |
| 8 | Low | BOM descriptions for `C18`, `C20`, `R4` and several 0402 resistors are copied from other parts, and manufacturer part numbers are empty | Fill in real manufacturer part numbers and supplier data before ordering |
| 9 | Low | Schematic title block (title, revision, author) is empty | Fill in title, revision and author on every sheet |
| 10 | Low | No fan tachometer input or over-temperature indicator | Consider for a future revision (3-wire fan support) |
