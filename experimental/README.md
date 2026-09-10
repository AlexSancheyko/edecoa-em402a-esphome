# Experimental builds

The stable/reference configuration is kept in the repository root as `edecoa_em402_WORKING_REFERENCE_v6_5.yaml`.

Current experimental target: TOTAL battery charging-current control for 60–100 A.

Observed command behavior on the tested inverter:

- `MCHGC050` → ACK, works.
- `MCHGC070` / `MCHGC080` / `MCHGC090` / `MCHGC100` → NAK.
- `MNCHGC0110` → ACK, works and QPIRI reports 110 A.
- The original EDECOA Wi-Fi adapter can set 60/70/80/90/100 A, therefore these values are supported by the inverter and the missing piece is the exact serial command format.

A later experimental build can test `MNCHGC0060`, `MNCHGC0070`, `MNCHGC0080`, `MNCHGC0090`, and `MNCHGC0100`, but these should not be treated as confirmed working until QPIRI readback verifies the setting.
