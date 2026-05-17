# Rooftop Solar Suitability Assessment - San Diego

## Project Overview

This project performs an end-to-end geospatial analysis to assess rooftop solar suitability for buildings in San Diego, California. The workflow identifies which rooftops are best suited for solar panel installation based on:

- **Solar Irradiance** (GHI - Global Horizontal Irradiance) sampled from PVGIS raster data
- **Roof Area** derived from building footprints
- **Shadow Proxy** estimated from building height data (taller nearby buildings = more potential shading)
- **Annual Energy Potential** calculated from irradiance x roof area

Buildings are classified into **High**, **Moderate**, and **Low** solar suitability categories, and results are exported with full geospatial attributes.

---

## How to Run the Notebook

### Prerequisites

Ensure the following folder structure is preserved exactly as submitted:

```
submission_folder/
    notebook/
        solar_suitability_simplified.ipynb   (main notebook - run this)
    data/
        boundaries/
            tl_2023_06_place.shp             (California places shapefile)
            (associated .dbf, .prj, .shx, etc.)
        buildings/
            california_buildings.geojson     (building footprints, ~3.4 GB)
        building_heights/
            san_diego_building_heights.geojson
        solar_irradiance/
            GHI.tif                          (primary irradiance raster)
            (other .tif files: DNI, DIF, GTI, PVOUT, OPTA, TEMP)
        cache/
            san_diego_buildings_cache.gpkg   (pre-clipped buildings cache)
        output/
            san_diego_solar_results.gpkg     (final processed dataset)
    README.md
```

### Steps to Run

1. **Open** `notebook/solar_suitability_simplified.ipynb` in Jupyter Lab or Jupyter Notebook.
2. **Do not modify any paths** - all paths are set relative to the notebook location using `Path('..').resolve()`. They resolve automatically as long as the folder structure above is intact.
3. **Run All Cells** - use `Kernel > Restart & Run All` to execute the notebook from scratch.
4. The notebook will:
   - Load the San Diego city boundary
   - Load (or use cached) building footprints clipped to San Diego
   - Join building height data
   - Sample GHI solar irradiance for each building
   - Compute a shadow penalty and annual energy estimate
   - Classify buildings into suitability tiers
   - Generate maps and export results

Note: The first run may take several minutes due to the large size of `california_buildings.geojson` (~3.4 GB). Subsequent runs use the cache (`san_diego_buildings_cache.gpkg`) and are significantly faster.

---

## Libraries Used

The following Python libraries are required. Libraries marked as "extra" are not covered in class and must be installed manually.

| Library | Purpose | Covered in Class |
|---|---|---|
| `geopandas` | Spatial data (GeoDataFrames, clipping, joins) | Yes |
| `pandas` | Tabular data manipulation | Yes |
| `numpy` | Numerical operations | Yes |
| `matplotlib` | Plotting and map visualization | Yes |
| `rasterio` | Reading GeoTIFF raster files (GHI, DNI, etc.) | Yes |
| `pyproj` | Coordinate Reference System (CRS) transformations | Yes |
| `contextily` | Adding basemap tiles (OpenStreetMap/CartoDB) to plots | No - install required |
| `pathlib` | Path handling (built-in Python, no install needed) | Yes |
| `warnings` | Suppressing non-critical warnings (built-in) | Yes |

### Installing Non-Class Libraries

```bash
pip install contextily
```

Or using conda:

```bash
conda install -c conda-forge contextily
```

---

## Data Sources

| Dataset | Source | Description |
|---|---|---|
| California Places Shapefile | [US Census TIGER/Line 2023](https://www.census.gov/cgi-bin/geo/shapefiles/index.php) | City boundaries for California |
| California Building Footprints | [Microsoft USBuildingFootprints](https://github.com/microsoft/USBuildingFootprints) | Building polygon geometries |
| Building Heights (San Diego) | [Overpass Turbo / OpenStreetMap](https://overpass-turbo.eu/) | Building height tags from OSM |
| Solar Irradiance Rasters | [PVGIS - EU Joint Research Centre](https://re.jrc.ec.europa.eu/pvg_tools/en/) | GHI, DNI, DIF, GTI, PVOUT rasters |

---

## Output Files

| File | Format | Description |
|---|---|---|
| `san_diego_solar_results.gpkg` | GeoPackage | Primary output - all buildings with suitability scores |
| `suitability_map.png` | PNG | Full suitability classification map |
| `suitability_zoomed.png` | PNG | Zoomed-in suitability map |
| `building_height_shadow_map.png` | PNG | Shadow proxy visualization |

---

## Notes

- The notebook uses `warnings.filterwarnings('ignore')` to suppress verbose but harmless deprecation warnings from geospatial libraries.
- All coordinate operations use **EPSG:32611** (UTM Zone 11N) for accurate area and distance calculations.
- Basemaps require an internet connection (fetched via `contextily`). The notebook handles basemap failures gracefully with `try/except`.
- The cache file (`san_diego_buildings_cache.gpkg`) stores pre-processed San Diego buildings. Delete it to force a full re-clip from the raw California dataset.

---

*Project by: Abdullah Jamil | Roll No: BSCS23136 | Course: Spatial Data Science | May 2026*
