# Week 3 note: reprojection, clipping, and quality checks

## CRS chosen and why
Reprojected all layers to **EPSG:32631 (WGS 84 / UTM zone 31N)**. The original GEBCO bathymetry and Marine Regions EEZ data came in EPSG:4326 (geographic, degrees). Degrees are not a fixed real-world distance, so calculating slope directly from unprojected data would give distorted, incorrect angles. UTM zone 31N covers this part of the Gulf of Guinea (around 3°E) and measures both horizontal position and depth in metres, letting slope be calculated accurately as true rise-over-run.

## What was reprojected and what was clipped
- **Reprojected:** bathymetry (GEBCO), EEZ boundary (Marine Regions), coastline (Natural Earth) — all from EPSG:4326 to EPSG:32631.
- **Clipped:** coastline only, from the full global dataset (4,133 features) down to 4 features overlapping the Nigeria EEZ.
- **Not clipped:** bathymetry and slope rasters. The bathymetry was downloaded pre-clipped to the study box (3.0-3.8°E, 5.8-6.7°N), so no trimming to the box was needed. However, the box is not fully inside the EEZ (see Check 2), so clipping to the EEZ would have removed the onshore part. An earlier attempt to clip against the EEZ polygon produced a raster with 0% valid pixels (likely a GML/gdalwarp cutline incompatibility) and was abandoned. The onshore pixels were therefore left in the analysis-ready layers.
- **Derived:** slope (0-22.7°) and slope_classified (gentle/moderate/steep) computed from the reprojected bathymetry.

## Five quality checks and results

1. **CRS check** — Confirmed bathymetry, EEZ, and coastline all report EPSG:32631. Pass.
2. **Extent/overlap check** - Bathymetry X range (500,000-588,463) falls within the EEZ's X range (464,980-1,127,805). Bathymetry's northern Y edge (740,657) exceeds the EEZ's northern Y edge (732,096) by about 8.5 km. This comparison uses bounding boxes only, so it cannot show how much of the raster actually lies inside the EEZ polygon. Visual inspection of the map shows the coastline crossing the study box roughly a third of the way down from the top edge, so a substantial part of the raster (roughly 30% by eye, not yet measured) is onshore and outside the EEZ. Flagged, not fixed in Week 3. The Week 4 intersection of the gentle-slope zone with the EEZ will measure the overlap properly.
3. **NoData/null check** — Bathymetry: 100% valid pixels. Slope: 98.04% valid. The ~2% gap is expected: edge pixels of any raster can't have slope computed since there's no neighbouring pixel beyond the boundary to compare against.
4. **Value range sanity check** — Bathymetry: -2,760m to 71m (plausible for shelf/upper slope terrain). Slope: 0° to 22.7° (plausible). This check caught a real problem, see below.
5. **Geometry validity check** — EEZ: 1/1 features valid. Coastline: 4/4 features valid. No corrupted or self-intersecting geometry.

## Problems found and how they were handled
- **Broken raster clip (fixed by change of approach):** clipping bathymetry against the EEZ polygon produced a raster with 0% valid pixels. Diagnosed as a cutline compatibility issue with the GML-derived EEZ geometry, not a fault in the source data (the EEZ polygon itself displayed and validated correctly). Resolved by skipping this clip, since the bathymetry was already bounded to the study area.
- **Wrong layer exported into GeoPackage (fixed):** the `slope` layer in the GeoPackage initially contained bathymetry values (-2,760 to 71) instead of slope values, caught during Check 4 (value range sanity). Root cause: the wrong source layer was selected during export. Fixed by deleting the incorrect layer and re-exporting the correct `slope_degrees` raster under the same name.
- **Raster extends onto land (flagged, not fixed):** see Check 2. An earlier version of this note described the northern overshoot as a thin strip of shoreline. That was wrong: it rested on the bounding-box comparison alone. The bathymetry, slope and slope_classified layers include onshore pixels, and flat land is classed as gentle slope, so the gentle class is overstated in the north until it is masked to the EEZ (done at the vector stage in Week 4).

## Where the analysis-ready file lives
`data/processed/geodev_analysis_ready.gpkg`, containing five layers: `bathymetry`, `slope`, `slope_classified`, `coastline`, `eez`.

Amended 2026-09-28: corrected the explanation of the northern extent overshoot (raster extends onto land).
