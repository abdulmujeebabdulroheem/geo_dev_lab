# Data note

## 1. Bathymetry — GEBCO 2026 Grid
- **Source:** GEBCO 2026 Grid — https://www.gebco.net/data_and_products/gridded_bathymetry_data/
- **Geometry type:** Raster (not vector)
- **Resolution / size:** ~450m per cell, 192 x 216 pixels, subset for 3.0-3.8°E, 5.8-6.7°N
- **Key values:** Depth relative to sea level, range -2,760m to 71m
- **Processing done:** Reprojected from EPSG:4326 to EPSG:32631 (UTM zone 31N) to allow accurate slope calculation in metres; slope raster derived from the reprojected file (range 0° to 22.7°)
- **Gaps/notes:** No missing data observed within the study extent. Coverage confirmed visually complete across the shelf and upper slope.

## 2. Maritime boundary — Nigeria EEZ
- **Source:** Marine Regions — https://www.marineregions.org (MRGID 8474)
- **Geometry type:** Polygon
- **Feature count:** 1 (single polygon covering the whole Nigerian EEZ)
- **Key columns:** `geoname` (Nigerian Exclusive Economic Zone), `mrgid` (8474), `territory1`/`sovereign1` (Nigeria), `pol_type` (200NM)
- **Gaps/notes:** `_ter2`/`_ter3`/`sovereign2`/`sovereign3` fields are NULL, expected since this record has no shared/disputed boundary with a second or third country. No missing or unexpected values found.

## 3. Coastline — Natural Earth
- **Source:** Natural Earth, 1:10m Coastline — https://www.naturalearthdata.com/downloads/10m-physical-vectors/
- **Geometry type:** LineString
- **Feature count:** 4,133 (global coverage, not clipped to the study area yet)
- **Key columns:** `featurecla` (all "Coastline"), `scalerank`, `min_zoom` (both cartographic display hints, not analytical values)
- **Gaps/notes:** No gaps for this study; dataset is used only for visual orientation/context, not analysis.

## 4. Subsea pipelines/cables — attempted, not used
- **Sources checked:** marineregions.org (no relevant download category found), EMODnet Human Activities (EU-focused, no West African coverage), OpenStreetMap via QuickOSM/Overpass API
- **QuickOSM result:** Query `man_made=pipeline` for "Lagos, Nigeria" returned 44 features (LineString/way geometry). Key columns: `man_made`, `location`, `usage`, `substance`, `name`, `diameter`.
- **Finding:** Inspection of the `location` attribute (values: overground, underground, overhead) and visual overlay against the study area confirmed these are onshore gas distribution lines (e.g. the ELPS/Escravos Lagos Pipeline System), not offshore subsea infrastructure.
- **Conclusion:** No open offshore pipeline/cable dataset was found for this area. This remains a known gap, noted for a possible later iteration rather than blocking this week's work.
