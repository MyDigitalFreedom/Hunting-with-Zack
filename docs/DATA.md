# Data sources and yearly updates

All data ships inside the app as versioned JSON in `public/data/`, so it works offline. Each file carries a `meta` block:

```json
{ "meta": { "source": "…", "licenceYear": "2026-27", "retrieved": "2026-10-30", "notes": "…" }, "data": … }
```

| Pack | Source | Update | How |
|---|---|---|---|
| `wmu.geojson` | Alberta WMU boundaries (the GeoJSON used by the prototype) | When boundaries change (rarely) | Re-project/simplify with `mapshaper`; keep properties `WMU`, `name` |
| `province/lakes/rivers.geojson` | Natural Earth admin-1, lakes, rivers (public domain) | Never | — |
| `seasons-YYYY.json` | **Alberta Hunting Regulations** PDF for the licence year | Every spring/summer | Extract the season tables per WMU and species (145 rows in 2026). Spot-check at least 5 WMUs by hand (2026 checks: 148, 248, 344, 504) |
| `rules-YYYY.json` | Same regulations: Sunday closures, CWD mandatory WMUs, special rules | Every year | 2026: `SUN_CLOSED` = 102–160, 624, 728, 730, 936; `CWD_MAND` = 51 WMUs |
| `harvest-YYYY-YYYY.json` | Alberta harvest estimate reports (whitetail, mule deer, elk, moose PDFs) | When the new year's reports are posted | Add the year; keep 4 years. Rows without a success % stay `null` (UI shows "—") |
| `towns.json` | 55 forecast towns with lat/lon | Rarely | Used for nearest-forecast point per WMU |
| Weather | Live: Open-Meteo or ECCC MSC GeoMet (ARCHITECTURE → Weather) | Live, cached | Respect licence and attribution |
| Sun, moon | Computed on device (NOAA sun formula, SunCalc-based moon) | — | Validated against the regulations' sunrise table within 1–4 min |

## Yearly checklist
1. Download the new regulations PDF. Extract seasons and rules into new `seasons-YYYY.json` and `rules-YYYY.json` files.
2. Run `npm run validate-data`. This schema-checks the packs and confirms every WMU in the GeoJSON has a season row for each species, or an explicit "no season".
3. Diff against last year's data and review every changed WMU by hand.
4. Add any new harvest year.
5. Update the season start (`S0`) and the year setting.
6. Release before the archery openers in late August.

## Accuracy statement (show this in the app)
> Season dates and rules are transcribed from the Alberta Hunting Regulations and may contain errors or miss mid-season changes. The official regulations and My Wild Alberta notices always take precedence. This app is not affiliated with the Government of Alberta.
