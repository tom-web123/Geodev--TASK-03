# Building an Analysis-Ready Road Network for Dar es Salaam

This repository contains the Dar es Salaam road network reprojected to EPSG:32737 (WGS 84 / UTM zone 37S) and clipped to the GADM administrative boundary.

**Research question:** Which administrative divisions in Dar es Salaam have the least access to primary/trunk roads, and what does that suggest about mobility disadvantage across the city?

**Contents:**
- `dar_analysis_ready.gpkg` — analysis-ready GeoPackage (`dar` boundary layer, `roads_clipped` road layer)
- `data_prep_note.md` — CRS decisions, reprojection/clip details, and the five quality checks

**Sources:** boundary from GADM, roads from a HOTOSM export.
