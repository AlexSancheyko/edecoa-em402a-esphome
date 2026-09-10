# Wiring

## Tested UART

```yaml
uart:
  id: inverter_uart
  tx_pin: GPIO3
  rx_pin: GPIO2
  baud_rate: 2400
  data_bits: 8
  parity: NONE
  stop_bits: 1
```

The ESP32-C3 logger uses USB Serial/JTAG so GPIO2 and GPIO3 remain available for the inverter UART.

## Connections

### ESP32-C3 ↔ MAX3232 TTL side

- GPIO3 TX → DIN / T1IN
- GPIO2 RX ← ROUT / R1OUT
- 3.3 V → VCC
- GND → GND

### MAX3232 RS-232 side ↔ inverter

- Inverter TX → RIN
- Inverter RX ← DOUT
- GND ↔ GND

Do not connect RS-232 voltage-level pins directly to ESP32 GPIO pins.

## Note about GPIO2

GPIO2 can be a strapping-related pin on ESP32-C3 boards. It is working in the tested setup,
but if your board has boot problems, check the exact board schematic and boot-state requirements.
