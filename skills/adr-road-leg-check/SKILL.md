---
name: adr-road-leg-check
description: Check dangerous goods for a road leg with the FreightUtils ADR tools — the ADR 1.1.3.6 small-load ("points") check for the whole load, limited-quantity (LQ) and excepted-quantity (EQ) relief, and the ADR Table A entry behind each UN number. Use when the user lists UN or ID numbers with quantities for a van, truck or trailer and asks whether the load is under the ADR small-load limit, how many points it is, whether it is "ADR", or whether LQ or EQ applies.
---

# ADR road-leg check

Every number, verdict and section reference in the answer comes from a FreightUtils tool result —
never from memory. Quote the tool's own `message`, `warnings` and `citation` rather than restating
the regulation in your own words.

## 1. Collect the lines — ask, never infer

For each dangerous-goods line on the vehicle you need:

- the **UN number** — 4 digits, optionally "UN"-prefixed; explosives keep the leading zero ("0004").
  An air waybill or NOTOC can carry an **ID-prefixed** number such as "ID 8000": pass it as given;
- the **quantity** and its **unit**, `L` or `kg`;
- the **packing group** (I, II or III), when the user has it.

If a quantity, a unit or the basis of the figure is missing or unclear, ask. Do not assume a
density, convert litres to kilograms, or take a gross shipping weight as the quantity. The
calculator's quantity help states the basis it counts: litres for liquids; net kg for solids and
for liquefied, refrigerated or dissolved gases; receptacle water capacity in litres for compressed
or adsorbed gases; articles as the mass of the articles without their packagings. A figure that
includes packaging is not that basis — ask for the net figure.

## 2. Look up each UN

Call `adr_lookup` with `un_number` for each line. Report the proper shipping name, class, packing
group, transport category, tunnel code and LQ / EQ values as returned. If the result holds more
than one row for the UN, keep every row — the bare UN does not choose one, and neither do you.

## 3. Run the small-load check once, with every line

Call `adr_exemption_calculator` with `items` — one item per line, each with its own `un_number`,
`quantity`, `unit` and, where known, `packing_group` (or `variant_index` from the lookup). Put the
whole load in **one** call: the check is for everything on the transport unit together. Never send
`un_number`, `quantity`, `packing_group`, `variant_index`, `unit` or `basis` at the top level beside
`items` — the calculator refuses that call.

Read the result:

- **`AMBIGUOUS_UN_VARIANT`** in `blocking_errors`, or a line with `withheld: true` — the UN has
  several Table A rows that differ. Show the `candidates` and ask the user for the packing group (or
  which variant applies), then call again. Never pick a row yourself.
- **Each line's `state`** — report `COUNTED`, `AIR_ONLY_ID`, `NOT_SUBJECT_TO_ADR` and `BLOCKED`
  exactly as returned, with that line's message or warning. An `AIR_ONLY_ID` line carries the
  condition its zero rests on; a `NOT_SUBJECT_TO_ADR` line (dry ice, UN 1845, for one) carries the
  conditions that still apply.
- **The verdict** — `exempt`, `total_points`, `threshold`, `message` and `warnings` as returned.
  `exempt: null` means no verdict was reached: say why, from the message, and never turn it into a
  yes or a no.

## 4. Limited or excepted quantities

When the user asks about LQ or EQ, or the goods are in small inner packagings, call `adr_lq_eq_check`
with `mode` `"lq"` or `"eq"` and items carrying the quantity **per inner packaging** with its unit
(`ml`, `L`, `g`, `kg`; for EQ, `inner_packaging_qty` per outer package). Report `overall_status` and
each item's `status` and `reason`. An `inconclusive` item — a mass against a volume limit, or the
reverse — needs the quantity in the unit the limit uses; ask for it rather than converting.

## 5. What every answer says

- It covers **only the goods the user listed**. Anything else dangerous on the same transport unit
  must be added, because the small-load check counts the whole load.
- It is a **reference check** from the FreightUtils tools, not compliance sign-off: classification,
  packaging, documentation and carrier acceptance stay with the consignor and the carrier, and the
  current ADR text governs.

Name the source the tool returns — its `citation` and `_source` — with the answer.
