---
name: freight-reference
description: Freight reference lookups with the FreightUtils tools — which airline issues an air waybill prefix, whether an AWB, container or IMO number passes its check digit, airport codes and the airports nearest a point, UN/LOCODE locations, shipping-container, air-cargo ULD and road-vehicle specifications, and what an unfamiliar identifier is. Use when the user gives a code, prefix or identifier and asks what or whose it is, whether it is valid, or what the equipment's dimensions and limits are.
---

# Freight reference

Look the answer up; never state a code, prefix or specification from memory. Several of these
identifiers are shared between more than one record — report what the tool returns, all of it.

## Airlines and air waybills

- **`airline_lookup`** — `prefix` (the 3-digit AWB prefix, e.g. "176"), `iata`, `icao`, `country`,
  or `query` for a name. A prefix or code can be held by more than one airline record: name every
  holder the result lists, never only the first. The dataset's provenance is pending independent
  verification; for an operationally critical code, say to confirm with IATA or ICAO or the carrier.
- **`validate`** — `value` + `type` (`awb`, `container`, `imo`) for one identifier, or `text` to find
  and check every identifier in a pasted line or email. A passing check digit means the number is
  well-formed (AWB modulus 7, ISO 6346 for containers, the IMO scheme). It does NOT mean the waybill
  was issued, the container exists or the vessel is active — say so whenever you report a pass.

## Airports and locations

- **`airport_lookup`** — `iata` (3 letters), `icao` (4 characters) or `query` (name or city).
  Reference only, not for navigation.
- **`nearest_airport`** — needs `latitude` and `longitude`; it does not turn a place name into
  coordinates. If the user gives only a place name, use `airport_lookup` with the name, or ask for
  coordinates.
- **`unlocode_lookup`** — `code` (5 characters: country + location, e.g. "NLRTM") or `query`,
  narrowed by `country` and `function_type` (`port`, `airport`, `rail`, `road`, `icd`, `border`).
  It is an administrative code list: confirm operational status with the port or authority.

## Equipment

- **`container_lookup`** — `type` slug (e.g. "20ft-standard", "40ft-high-cube"), or none for all
  ten types; add item dimensions to see how many items fit. Specifications are manufacturer-typical
  and vary by lessor and line. A container NUMBER is a `validate` question, not this one.
- **`uld_lookup`** — `type` as the IATA code ("AKE", "PMC") or slug; `category` and `deck` filter
  the list. Pallets (PMC, PAG and family) have no internal dimensions: report
  `max_build_up_height_cm` and never multiply a pallet's dimensions into a volume. Provenance is
  pending — say to confirm critical dimensions with the carrier.
- **`vehicle_lookup`** — `slug`, or filter by `category` (`articulated`, `rigid`, `van`) and
  `region` (`EU`, `US`). Typical specifications; the legal payload is set by the vehicle's plated
  weights.

## An identifier you can't place

**`resolve_reference`** with `q` — ONE token (up to 32 characters), not a whole line. It returns
typed candidates; more than one candidate is a normal answer ("LHR" is an airport and an airline
code). Present the candidates and ask which the user means when it matters, instead of choosing.

If a call returns a limit error, tell the user when the allowance resets (`reset_at`) rather than
retrying.
