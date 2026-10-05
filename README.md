# FreightUtils for Claude

Freight answers in Claude, each with its source named. This plugin pairs the FreightUtils
connector — 25 read-only tools at `https://www.freightutils.com/api/mcp` — with four skills that
teach Claude how freight people ask and which tool answers what:

| Skill | Use it for |
|---|---|
| `adr-road-leg-check` | Dangerous goods on a van, truck or trailer: the ADR 1.1.3.6 small-load points check for the whole load, limited and excepted quantities, the UN entries behind them |
| `freight-calculations` | Cubic metres, loading metres, air chargeable weight, boxes on a pallet, mixed consignments, unit conversions, CO2e for a leg |
| `customs-and-trade` | HS codes, UK import duty and VAT estimates from the live GOV.UK Trade Tariff, Incoterms 2020, EU ICS2 goods-description checks |
| `freight-reference` | Airlines by AWB prefix, AWB / container / IMO check digits, airports, UN/LOCODE locations, containers, ULDs, vehicles, and an identifier you can't place |

## Use it

Install the plugin, then ask in plain words:

- "200 litres of petrol and 50 litres of UN 1263 paint, packing group III, on one van — under the ADR small-load limit?"
- "Chargeable weight for 3 boxes, 120 × 80 × 100 cm, 640 kg in total, by air?"
- "UK duty on £12,000 of HS 8471 30 from China?"
- "Which airline has AWB prefix 176, and is 176-12345675 a valid AWB number?"

The plugin needs no account and no API key. On claude.ai, the desktop and mobile apps and Cowork,
connect the bundled FreightUtils connector from the plugin's **Connectors** tab; Claude Code
connects to it directly. If you already added the FreightUtils connector, it is the same server and
the same tools.

## Limits

Calls made through Claude's apps reach FreightUtils from Anthropic's network and share one
allowance of 5,000 tool calls a day across all Claude users, resetting at 00:00 UTC. Claude Code
connects from your own machine and gets that address's own allowance of 25 tool calls a day. A
FreightUtils API key sent as an `X-API-Key` header applies its own plan's limits instead. Limits and
details: <https://www.freightutils.com/mcp#use-in-claude>.

## What the answers are

Reference information and deterministic calculations from published sources — UNECE ADR 2025, the
WCO HS 2022 nomenclature, the GOV.UK Trade Tariff, ICC Incoterms 2020 summaries, UN/LOCODE and
others — with the source and edition named in every tool result. They are not legal, customs or
dangerous-goods compliance advice: classification, documentation and carrier acceptance stay with
the consignor and the carrier, and the current published text governs.

## Privacy

This plugin contains only instructions (skills) and the address of the FreightUtils connector; it
runs no code on your machine and stores nothing.

**Data handling:** tool inputs go only to `www.freightutils.com`. A tool call carries just the tool's
own inputs — dimensions, codes, quantities, a goods description, coordinates — which are used to
compute the answer and are not stored. The one onward lookup is a UK duty estimate, for which
FreightUtils asks the public GOV.UK Trade Tariff about the commodity code and nothing else.
FreightUtils receives no Claude account details, name, email or conversation content.

Full privacy policy: <https://www.freightutils.com/privacy>

## Support

contact@freightutils.com · <https://www.freightutils.com/contact>

## License

MIT — see [LICENSE](LICENSE).
