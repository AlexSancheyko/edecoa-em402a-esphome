# Protocol notes

This file documents **observed behavior on the tested EDECOA EM-402A**.

## Working polling commands

- `QPIRI`
- `QPIGS`
- `QMOD`
- `QFLAG`

## QPIRI charging-current fields

Observed QPIRI sequence includes:

```text
... battery_type  AC_charge_current  TOTAL_charge_current ...
```

Example:

```text
... 2 10 050 ...
```

means:

- battery type = 2
- AC / utility charge-current limit = 10 A
- total charge-current limit = 50 A

## AC / utility charging current

The tested inverter accepts the short `MUCHGCxxx` command family.

Examples:

```text
MUCHGC010
MUCHGC020
...
MUCHGC080
```

The AC current setting works across the tested range once TOTAL current is high enough.

## TOTAL charging current

Observed behavior:

| Requested TOTAL | Command tested | Result |
|---:|---|---|
| 50 A | `MCHGC050` | ACK / works |
| 60 A | `MCHGC060` | not accepted in testing |
| 70 A | `MCHGC070` | NAK |
| 80 A | `MCHGC080` | NAK |
| 90 A | `MCHGC090` | NAK |
| 100 A | `MCHGC100` | NAK |
| 110 A | `MNCHGC0110` | ACK / works |

The official EDECOA Wi-Fi module can set the intermediate values, so 60–100 A are supported
by the inverter itself. The exact command or command variant used by the official adapter
still needs to be captured.

## Reverse-engineering target

The cleanest next step is passive serial sniffing of the official EDECOA Wi-Fi module while
changing TOTAL current through the mobile app, then reproducing those exact commands in ESPHome.
