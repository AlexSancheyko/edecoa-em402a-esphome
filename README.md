# EDECOA EM-402A — ESPHome / Home Assistant integration

Community ESPHome configuration for integrating an **EDECOA EM-402A 24 V hybrid inverter**
with **Home Assistant** through **RS-232**, using an **ESP32-C3** and a **MAX3232** level converter.

This project is based on the `pip2424mse1` external component from
[syssi/esphome-pipsolar](https://github.com/syssi/esphome-pipsolar).

## Status

Current reference firmware:

**`edecoa_em402_WORKING_REFERENCE_v6_5.yaml`**

Confirmed on the tested EDECOA EM-402A unit:

- QPIRI / QPIGS / QMOD / QFLAG polling works.
- Home Assistant telemetry works.
- Output source priority control works.
- Charger source priority control works.
- AC / utility battery charging-current control works across the tested range up to **80 A**.
- Total battery charging-current control currently has a protocol quirk:
  - **10–50 A**: working with the tested command family.
  - **60–100 A**: not yet solved over the custom RS-232 integration.
  - **110 A**: working.
- The original EDECOA Wi-Fi module can set the full total-charge range, so the inverter itself
  supports those intermediate values. The remaining task is to determine the exact command
  used by the official module for 60–100 A.

## Hardware

Tested setup:

- EDECOA EM-402A hybrid inverter
- ESP32-C3 SuperMini / compatible ESP32-C3 board
- MAX3232 RS-232 ↔ TTL converter
- Home Assistant
- ESPHome 2026.8.x

## Wiring

ESP32-C3 side:

| ESP32-C3 | MAX3232 TTL side |
|---|---|
| GPIO3 TX | DIN / T1IN |
| GPIO2 RX | ROUT / R1OUT |
| 3.3 V | VCC |
| GND | GND |

RS-232 side:

- Inverter TX → MAX3232 RIN
- Inverter RX ← MAX3232 DOUT
- Common ground

UART settings:

- **2400 baud**
- **8 data bits**
- **No parity**
- **1 stop bit**

See [`docs/wiring.md`](docs/wiring.md) for details.

## ESPHome setup

1. Copy `edecoa_em402_WORKING_REFERENCE_v6_5.yaml` to your ESPHome configuration directory.
2. Create `secrets.yaml`.
3. Add your Wi-Fi credentials:

```yaml
wifi_ssid: "YOUR_WIFI_SSID"
wifi_password: "YOUR_WIFI_PASSWORD"
```

4. Compile and flash the ESP32-C3.
5. Add the ESPHome device to Home Assistant.

The configuration intentionally keeps credentials in `secrets.yaml`; **do not commit that file**.

## Important protocol notes

Two different charging-current limits exist:

- **AC / utility charging current** — maximum contribution from the grid charger.
- **TOTAL charging current** — maximum combined battery charging current from PV + AC.

Therefore, AC charging current cannot effectively exceed the TOTAL charging-current limit.

Confirmed examples from the tested inverter:

```text
AC / utility:
MUCHGC010 ... MUCHGC080

TOTAL:
MCHGC050      -> accepted, QPIRI changes to 050
MCHGC070      -> NAK
MCHGC080      -> NAK
MCHGC090      -> NAK
MCHGC100      -> NAK
MNCHGC0110    -> accepted, QPIRI changes to 110
```

See [`docs/protocol-notes.md`](docs/protocol-notes.md).

## Experimental firmware

Experimental builds are kept under [`experimental/`](experimental/).

Do not assume an experimental build is safer or more complete than the reference v6.5 firmware.

## Credits

- [syssi/esphome-pipsolar](https://github.com/syssi/esphome-pipsolar) — ESPHome PIP/Axpert-compatible protocol component.
- EDECOA EM-402A community testing and reverse engineering.

## Disclaimer

This is an independent community project and is not an official EDECOA integration.

Incorrect inverter settings can affect battery charging and power-system operation.
Verify limits for your own battery, BMS, wiring and inverter model before changing settings.
