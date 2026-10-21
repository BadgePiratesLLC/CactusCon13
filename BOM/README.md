# CC13 Bill of Materials

Exported per CactusCon's request (Nexus `def76d61`, filed by Jared, 2026-10-16).

## Files

- **`CC13-MainBoard-BOM.csv`** — main badge board (`CAD/Project-CC13.kicad_sch`),
  37 BOM lines (grouped by Value+Footprint+MFG_PN), columns: `Reference, Qty,
  Value, Footprint, MPN, LCSC#, Manufacturer, DNP`.

- **Outer board (`CAD-OuterBoard/*`, 4 variants: Attendee/Mafia/Speaker/Staff)
  — no BOM file.** Each variant's `.kicad_pcb` has no matching schematic and
  carries zero real components: all footprints present are silkscreen
  art/logo graphics (background art, badge-team text, skull logo) with no
  parts to place or source. Confirmed by inspecting the footprint list on all
  four variant `.kicad_pcb` files — same conclusion across all four.

## How this was generated

```
kicad-cli sch export bom \
  --fields "Reference,Value,Footprint,QUANTITY,MFG,MFG_PN,Vendor1,Vendor1_PN,DNP" \
  --labels "Reference,Value,Footprint,Qty,MFG,MFG_PN,Vendor1,Vendor1_PN,DNP" \
  --group-by "Value,Footprint,MFG_PN" \
  -o main_raw.csv CAD/Project-CC13.kicad_sch
```

KiCad walks the full sheet hierarchy (`Max17048.kicad_sch`, `Power.kicad_sch`,
`LEDs.kicad_sch`, `USB_Connections.kicad_sch`, `RotaryEncoders.kicad_sch`,
`Screens.kicad_sch`, `MicroSDCard.kicad_sch`) automatically — this is the full
badge BOM, not just the root sheet.

Most symbols on this schematic already carry per-instance `MFG`/`MFG_PN`/
`Vendor1`(distributor)/`Vendor1_PN` properties — those were used directly.
Where a row's `MFG`/`MFG_PN` was blank, it was cross-referenced against
`BadgePirates_Common_Library/BadgePiratesSymbol_Library/BadgePiratesSymbolLibrary.kicad_sym`
by symbol match (one hit: `BZ1`, filled from the library's `Buzzer_FUET-9032`
symbol). **No part number was guessed** — anything not found in either the
schematic or the Common Library was left blank and is listed below.

`LCSC#` is populated only where `Vendor1 == LCSC` on that line; rows sourced
from DigiKey or Mouser have a real `MPN` but no LCSC# (LCSC doesn't stock
that part under this MPN as far as the schematic/library record directly
confirm) — see the blank list.

### Blank / flagged lines (8 of 37)

| Ref(s) | Value | Issue |
|---|---|---|
| BT1, BT2 | Battery (14500) | MPN present (Keystone 53), sourced via DigiKey (36-53-ND) — no LCSC# |
| BZ1 | Buzzer | MPN filled from Common Library (FUET-9032, Fuet Electronics) — no LCSC#/distributor PN in either source |
| D3-D8 | LED (Osram reverse-mount) | MPN present (KingBright AA3528SYCKT09), sourced via Mouser — no LCSC# |
| J3 | ILI9341V header | DNP. No MFG/MFG_PN in schematic; no Common Library match for `Connector_PinHeader_2.54mm:PinHeader_1x14_P2.54mm_Vertical` by this value — generic header, not sourced |
| SW4 | SW_Enter | No MFG/MFG_PN in schematic; no Common Library match for footprint `SW_SPST_TL3305C(small)` |
| SW5 | SW_Back | Same as SW4 |
| SW6 | RotaryEncoder_Switch | No MFG/MFG_PN in schematic; no Common Library match for footprint `SIQ-02FVS3_Encoder` |

None of these were guessed — Kevin needs to supply MPN/LCSC# for SW4/SW5/SW6/J3
and (optionally) an LCSC-stocked equivalent for the DigiKey/Mouser-sourced
lines if an all-LCSC BOM is needed for a specific vendor quote.

## Tooling versions

- KiCad: `kicad-cli` 10.0.6
- Source commit: `8501a6f` (public `BadgePiratesLLC/CactusCon13`, 2025-03-15)
