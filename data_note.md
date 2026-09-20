# Data Preparation Note — Dar es Salaam Road Network

**Data sources:**
- dsm.gpkg (administrative boundary) — GADM, via QGIS
- highway_road_class.gpkg (road network) — HOTOSM export

**Analysis-ready file:** dar_analysis_ready.gpkg (delivered alongside this note), with two layers:
- dar — 73-feature study-area boundary (POLYGON)
- roads_clipped — 60,184-feature road network, clipped to that boundary (LINE)

## CRS chosen and why
**EPSG:32737 — WGS 84 / UTM zone 37S.** It's the correct UTM zone for Dar es Salaam, gives metre units making computations easy to do for example distances from one point to another.

## What I reprojected and what I clipped
- **Reprojected:** dsm.gpkg- (the boundary), from its true CRS, EPSG:4326 (geographic lon/lat), to EPSG:32737, so it's measurable and aligned with the road layer.
- **Clipped:** highway_road_class.gpkg- (the road network), to the boundary in dsm.gpkg.

## Result of each of the five quality checks
1. **What is its coordinate system, is it the right one?** — highway_road_class.gpkg was tagged EPSG:4326 but its coordinate values were impossible as degrees; I corrected them to EPSG:32737. dsm.gpkg's EPSG:4326 tag was correct, and was reprojected to EPSG:32737 (UTM 37S — the right working CRS for Dar es Salaam). Final output: both layers srs_id = 32737. 
2. **Are there empty value fields or values?** — NAME_3/TYPE_3 populated for all 73 dar features; highway populated for all 60,184 roads; name missing for 55,929 roads (93%). 
3. **Are there duplicate features?** — 0 duplicate fid's in either layer, 0 duplicate osm_id`s among roads. 
4. **Does the geometry look valid?** — minimum vertex counts, emptiness. dar: 0 unclosed/empty rings out of 73. roads_clipped: 0 empty/degenerate parts out of 60,184. 
5. **Does the coverage include your own study area?** — Not fully — the two layers don't share the same eastern extent. The boundary layer extends several tens of kilometres further east than the road layer does, so part of the study area (a few eastern divisions) falls outside the area the road data actually covers.

## Problems found, and whether fixed or flagged
- **highway_road_class.gpkg's CRS  was** (declared 4326, rather than actually UTM 37S) — **fixed**, corrected, to WGS84 37S EPSG: 32737
- **dsm.gpkg was in geographic coordinates**, not usable for metric analysis — **fixed**, reprojected to EPSG:32737.
- **93% of roads have no name attribute** — **flagged**, not fixed (a real OSM data gap).
- **Road coverage doesn't reach the eastern edge of the study area** (Somangira, Pemba Mnazi, Kimbiji divisions) — **flagged**, not fixed. This is a real gap in the HOTOSM export, not something clipping removed.

