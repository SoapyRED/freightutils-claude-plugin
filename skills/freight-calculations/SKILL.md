---
name: freight-calculations
description: Freight maths with the FreightUtils calculators — cubic metres (CBM), loading metres (LDM) on a trailer, air chargeable weight, how many boxes fit on a pallet, totals for a mixed consignment by sea, air or road, unit conversions and the CO2e of a transport leg. Use when the user gives dimensions, weights, pallet counts or a distance and asks for volume, trailer space, billable weight, a pallet plan, a consignment summary or emissions.
---

# Freight calculations

Pick the tool by what the user is asking, collect its inputs, and report the result with the
formula and source the tool returns. Never work the arithmetic out yourself instead of calling the
tool, and never fill in a missing dimension or weight — ask for it.

## Which tool

| The user wants | Tool | Inputs to collect |
|---|---|---|
| Volume | `cbm_calculator` | `length_cm`, `width_cm`, `height_cm` of ONE piece; `pieces` |
| Air billing weight | `chargeable_weight_calculator` | per-piece `length_cm`, `width_cm`, `height_cm`; `gross_weight_kg` for the WHOLE shipment; `pieces`; `factor` if not 6,000 |
| Trailer length used (road) | `ldm_calculator` | `pallet` preset (`euro`, `uk`, `half`, `quarter`) or `length_mm` + `width_mm`; `quantity`; `stackable` + `stack_height` (2 or 3); `weight_kg` PER PALLET; `vehicle` |
| Boxes on one pallet | `pallet_fitting_calculator` | pallet `pallet_length_cm`, `pallet_width_cm`, `pallet_max_height_cm` (deck height defaults to 15 cm); box `box_length_cm`, `box_width_cm`, `box_height_cm`; optional `box_weight_kg`, `max_payload_kg`, `allow_rotation` |
| Several different lines, one mode | `consignment_calculator` | `mode` (`sea`, `air`, `road`); `lines[]`, each with `quantity`, `dims {l, w, h, unit}`, `weight {value, unit}` |
| Everything at once, with a vehicle or container suggestion and UK duty | `shipment_summary` | `mode`; `items[]` with dims in cm, weight in kg, quantity |
| One conversion | `unit_converter` | `value`, `from`, `to` |
| CO2e for a leg | `emissions_calculator` | `mass` (+ `mass_unit`), `distance_km`, `mode`; optional `region`, `sub_mode`, `basis` |

## Pitfalls by mode

- **Air.** Chargeable weight is the greater of actual gross weight and volumetric weight. The tool
  defaults to the IATA divisor of 6,000; express integrators commonly bill on 5,000 — ask which the
  carrier uses when it matters, and pass it as `factor`. Dimensions are per piece, the gross weight
  is for the whole shipment.
- **Sea.** Revenue tonnes are the greater of weight and volume at 1 CBM = 1 tonne (W/M):
  `consignment_calculator` with `mode: "sea"` gives them; `chargeable_weight_calculator` is air only.
- **Road.** LDM takes the pallet footprint in millimetres and divides by the trailer's 2.4 m width;
  a standard articulated trailer is 13.6 LDM. A load is only treated as stacked when `stackable` is
  true; `fits` is about trailer LENGTH — give `weight_kg` (per pallet) to see the payload too. The
  `rigid10` vehicle preset is deprecated: use `custom` with `vehicle_length_m`.
- **Pallets.** The fit is a geometric best effort in aligned rows — it does not model interlocked
  patterns, carton strength or overhang.
- **Emissions.** Pass the ACTUAL gross mass, not chargeable or volumetric weight, and a distance the
  user gives — the tool does not route or geocode. It is an estimate from open factors (DEFRA, EPA,
  ADEME), not an audited carbon report.
- **Conversions.** `chargeable_kg` and `freight_tonnes` are valid only from `cbm`.

## Reporting

State the figure, the inputs it was computed from and any warning or flag the tool returned.
`consignment_calculator` flags are advisory and never say a shipment is permitted or compliant —
pass them on as they are. If a call returns a limit error, tell the user when the allowance resets
(`reset_at`) rather than retrying.
