# Data Preparation Note — Dar es Salaam Road Network

**Data sources:**
- `dsm.gpkg` (administrative boundary) — GADM, via QGIS
- `highway_road_class.gpkg` (road network) — HOTOSM export

**Analysis-ready file:** `dar_analysis_ready.gpkg` (delivered alongside this note), with two layers:
- `dar` — 73-feature study-area boundary (MULTIPOLYGON)
- `roads_clipped` — 60,184-feature road network, clipped to that boundary (MULTILINESTRING)

## CRS chosen and why
**EPSG:32737 — WGS 84 / UTM zone 37S.** It's the correct UTM zone for Dar es Salaam (central meridian 39°E), gives metre units, and both source layers' actual coordinates were already consistent with it once the CRS tags were corrected (see below).

## What I reprojected and what I clipped
- **Reprojected:** `dsm.gpkg` (the boundary), from its true CRS, EPSG:4326 (geographic lon/lat), to EPSG:32737, using the Transverse Mercator equations for UTM 37S.
- **Clipped:** `highway_road_class.gpkg` (the road network), to the boundary in `dsm.gpkg`.

## Result of each of the five quality checks
1. **What is its coordinate system, is it the right one?** — `highway_road_class.gpkg` was tagged EPSG:4326 but its coordinate values (e.g. 526357.5, 9,244,841.5) were impossible as degrees; tag corrected to EPSG:32737. `dsm.gpkg`'s EPSG:4326 tag was correct, and was reprojected to EPSG:32737 (UTM 37S — the right working CRS for Dar es Salaam). Final output: both layers `srs_id = 32737`. ✅
2. **Are there empty value fields or values?** — `NAME_3`/`TYPE_3` populated for all 73 `dar` features; `highway` populated for all 60,184 roads; `name` missing for 55,929 roads (93%). ⚠️
3. **Are there duplicate features?** — 0 duplicate `fid`s in either layer, 0 duplicate `osm_id`s among roads. ✅
4. **Does the geometry look valid?** — checked ring closure, minimum vertex counts, emptiness. `dar`: 0 unclosed/empty rings out of 73. `roads_clipped`: 0 empty/degenerate parts out of 60,184. ✅
5. **Does the coverage include your own study area?** — mostly, but not fully. `dar`'s full extent is 500,894–602,637 E; the road network only reaches as far as x≈560,826 E. Three eastern divisions (Somangira, Pemba Mnazi, Kimbiji) extend beyond that — Somangira alone reaches to 602,637 E, ~42 km past the last road vertex. That stretch of the study area has no HOTOSM road coverage at all. ⚠️

## Problems found, and whether fixed or flagged
- **`highway_road_class.gpkg`'s CRS tag was wrong** (declared 4326, actually UTM 37S) — **fixed**, tag corrected, no coordinate change needed.
- **`dsm.gpkg` was in geographic coordinates**, not usable for metric analysis — **fixed**, reprojected to EPSG:32737.
- **93% of roads have no `name` attribute** — **flagged**, not fixed (a real OSM data gap, not something this processing introduced).
- **Road coverage doesn't reach the eastern edge of the study area** (Somangira, Pemba Mnazi, Kimbiji divisions) — **flagged**, not fixed. This is a real gap in the HOTOSM export, not something clipping removed; there's no additional road data to fill it with. Worth keeping in mind for the mobility-access question, since those divisions will look artificially road-poor.
- Clip result: 0 roads dropped, 5 trimmed at the boundary, 60,179 unchanged.

## Where the analysis-ready file lives
`dar_analysis_ready.gpkg`, in the same location as this note.
