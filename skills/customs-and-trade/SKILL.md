---
name: customs-and-trade
description: Customs and trade questions with the FreightUtils tools — finding an HS commodity code, estimating UK import duty and VAT from the live GOV.UK Trade Tariff, explaining an Incoterms 2020 rule, and checking a goods description against the EU ICS2 stop-words list before an entry summary declaration. Use when the user asks for a tariff or HS code, what duty or VAT they would pay importing into the UK, who pays or carries the risk under an Incoterm, or whether a goods description will pass ICS2.
---

# Customs and trade

Every answer here is an estimate or a reference — say so, and say who confirms it: a customs broker
or HMRC for a UK duty figure, the ICC rules and the contract for an Incoterm, the customs authority
for a classification.

## HS codes — `hs_code_lookup`

- Search with `query` (min 2 characters), look up a `code` (2 to 6 digits, with its hierarchy), or
  browse a `section` (Roman numeral I to XXI).
- The dataset is the 6-digit international HS 2022 level. National tariff lines (8 to 10 digits) and
  duty rates are set per country.
- The search matches the official descriptions, so everyday words can find nothing ("laptop" finds
  nothing; "automatic data processing machines" does). A result of zero rows is a valid answer: try
  the formal tariff wording before saying no code exists.
- A code found here is indicative, not a binding classification ruling.

## UK duty and VAT — `uk_duty_calculator`

- Needs `commodity_code` (6 to 10 digits), `origin_country` (ISO 2-letter) and `customs_value` in
  GBP; `freight_cost`, `insurance_cost` and `incoterm` refine the customs value.
- Rates are read live from the GOV.UK Trade Tariff on each call. A 6-digit code may not be a
  declarable line: if the tool says so, ask the user for the full commodity code, or show the
  candidate lines rather than choosing one.
- Preferential rates that depend on proof of origin, and anti-dumping or other measures, come back
  in `warnings` — they are flagged, not applied. Pass them on.
- Report the duty rate, duty, VAT, total import taxes and landed cost as returned, with the
  tool's qualifier: an estimate, not a customs ruling — confirm with a customs broker or HMRC.

## Incoterms 2020 — `incoterms_lookup`

- `code` for one rule, `category` (`any_mode` or `sea_only`) for a list, or neither for all eleven.
- FAS, FOB, CFR and CIF are for sea and inland waterway only; if the user applies one to another
  mode, point out the mode the rule is for, from the tool's record.
- The records are summaries; the ICC publication is the binding text and the contract's own wording
  prevails.

## ICS2 goods descriptions — `ics2_check`

- Pass the goods `description` exactly as it would be filed.
- Each flagged term comes with a note: a standalone stop-word means automatic rejection, an embedded
  one means the description should be made more specific. Suggest a more specific wording when one
  is obvious from what the user told you, and say it is a suggestion.
- `clean: true` means no listed term matched — it does not guarantee acceptance, and the tool gives
  no accepted / rejected verdict. It is a reference check, not an ENS filing.

If a call returns a limit error, tell the user when the allowance resets (`reset_at`) rather than
retrying.
