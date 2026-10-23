# ga-district-information

Crosswalks from Georgia ZIP codes, counties and census tracts to the state Senate, state House and U.S. House districts they fall in, and from each district back to the counties it covers, maintained by [VoteGA.org](https://votega.org). They exist so that the next person who needs "which districts touch this place?" doesn't have to rebuild the join.

Files are generated and published automatically from the [votega.org](https://github.com/Votega/votega.org) repository. Please don't edit them by hand.

## Read this before you use it

- **A ZIP code is not a district.** The ZIP table lists every district that *touches* a ZIP, because a ZIP can span several. Only a street address identifies the districts a voter lives in. Please keep that distinction in anything you build on it.
- **A district is not a county.** Most House districts, and many Senate and congressional ones, take only part of a county. Every county and district row carries the population on both sides and a `coverage` flag (`full` when the whole county lies in the district, `partial` otherwise), so a list of a district's counties is never a claim that the district represents the whole county.
- **Nothing is cut off.** Every district with residents in a ZIP is listed, ranked by estimated share of the ZIP's residents, largest first. A small share is information, not a reason to drop the row: someone living in a ZIP's smallest sliver still votes in that district.
- **ZIP shares are estimates.** They combine HUD's residential-address counts with 2020 Census population by district, so treat them as an indication of which district is likelier, never as a certainty about a person.
- **PO-box ZIPs carry no districts.** A PO-box-only ZIP says nothing about where someone lives, so it is listed with `kind: "po-box"` and no districts.
- **County codes are 5-digit FIPS.** Every `county_fips` and every county key is the full state-plus-county code (`13013` for Barrow), the form the Census Bureau and Georgia Civic Data use, so these files join to theirs directly. Releases before 2.0.0 used the 3-digit suffix (`013`) under the same names; `data/county-codes.csv` maps between the two.

## Files

Each dataset comes as JSON, CSV and a JSON Schema. The CSVs are flattened views of the JSON, one row per place and district. Two files are CSV only: `data/district-counties.csv` and `data/county-codes.csv`.

| Dataset | Files | What it is |
|---|---|---|
| ZIP codes | `data/zips.json` · `data/zip-districts.csv` · `data/zip-counties.csv` · `data/zips.schema.json` | For each Georgia ZIP: the counties and the state Senate, state House and U.S. House districts it touches, each ranked by estimated share of the ZIP's residents. |
| Counties | `data/counties.json` · `data/county-districts.csv` · `data/counties.schema.json` | For each county: its 2020 population in each district, the share of the county that is, and whether the county lies wholly (`full`) or partly (`partial`) in that district. |
| Districts | `data/district-counties.csv` | The county table from the district's side: one row per chamber, district and county, with the county's 2020 population in the district, the share of the district that lies in that county, the share of the county the district holds, and `coverage`. |
| Census tracts | `data/tracts.json` · `data/tract-districts.csv` · `data/tracts.schema.json` | For each tract: its 2020 population in each district. This is what the other two are built from; use it to build crosswalks for your own geography. |
| County codes | `data/county-codes.csv` | All 159 counties: the 5-digit FIPS (`county_fips`), the 3-digit suffix earlier releases used (`county_code_3digit`), the county name and VoteGA's slug. |

Field definitions are in the schema files. `metadata` in each JSON file records the sources, the district plans, the Census vintage and, for ZIPs, the HUD release it was built from.

## How it is made

- **Counties and tracts** come from the Georgia General Assembly's Office of Legislative and Congressional Reapportionment block-equivalency files for the current state Senate, state House and congressional plans, joined to the Census Bureau's 2020 block populations. They are built only when the result matches the Office's own published county reports and district totals exactly.
- **ZIP codes** combine HUD's ZIP-to-tract residential-address ratios (from the HUD-USPS ZIP Code Crosswalk) with the tract table above. USPS data identifies which ZIPs are real Georgia delivery ZIPs and which are PO-box only. County shares come from HUD directly.
- Districts are those of the plans named in `metadata`. If Georgia redraws its maps, the county and tract tables are rebuilt and the ZIP table follows.

## Updates

The ZIP table is checked monthly and changes when HUD publishes a new release. The county and tract tables change only when the district plans or the Census vintage change. If a refresh fails, the files are left as they were.

## Changes

- **2.0.0** Every county code is now the 5-digit FIPS (`13013`) instead of the 3-digit suffix (`013`): the `county_fips` columns in all four CSVs that have one, the keys of `counties.json`, the county keys of `tracts.json`, and the `fips` field in `zips.json`. This breaks anything that joined on the old codes; `data/county-codes.csv` maps old to new. Added `data/district-counties.csv`, `data/county-codes.csv`, and a `coverage` column on `data/county-districts.csv`.
- **1.0.0** First release: ZIP, county and tract crosswalks.

## Reporting corrections

Please open an issue here with the ZIP, county or tract and what looks wrong. Corrections are made in the generating code at VoteGA.org, not in these files.

## License

[CC-BY-SA 4.0](https://github.com/Votega/ga-district-information?tab=CC-BY-SA-4.0-1-ov-file). See `NOTICE.md` for the attribution to use and for the upstream sources.
