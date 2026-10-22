# Bring-Up and Testing Plan

Use a current-limited bench supply (start at 100 mA limit) and **do not connect a fan or the MCU programmer until steps 1–4 pass**.

| Step | Check | Expected result |
|---|---|---|
| 1 | Visual inspection, continuity from the DC jack to GND with power off | No short between `VCC` and GND |
| 2 | Apply 20 V, measure `VCC` at `C6` | ≈ 20 V, low idle current |
| 3 | Measure 12 V rail (`C4`) | ≈ 12 V (±3%) |
| 4 | Measure 5 V rail (`C23`) and 3.3 V rail (`C8`) | ≈ 4.95 V and ≈ 3.3 V |
| 5 | Red LED `D3` | Lights when 3.3 V is present |
| 6 | Connect ST-LINK to `J3` and read device ID | STM32F411 detected |
| 7 | Read `PA4` voltage at room temperature | ≈ 0.75 V at 25 °C (TMP36) |
| 8 | Warm the sensor by hand | Voltage rises by about 10 mV per °C |
| 9 | Drive `PA6` high, no fan connected | Drain of `Q1` pulled to ≈ 0 V |
| 10 | Connect fan, test 0 %, 50 %, 100 % duty | Fan speed follows duty; `D4` clamps the turn-off spike |
| 11 | Thermal check at full fan load | Buck ICs, inductors, `D4` and `Q1` within safe temperature; sensor reading not skewed by board heating |
| 12 | Test across 20 V, 22.5 V and 25 V | Rails remain in regulation |

Record the measured values in a table (date, board serial, rail voltages, idle current, fan current) and commit it to the repository under `docs/`.
