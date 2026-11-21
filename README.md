# Stream-network rainfall–runoff model with sequential Bayesian parameter calibration

Distributed rainfall–runoff model for a USGS-gauged basin. Uses hourly MRMS radar rainfall on each ~1 km cell, which is routed to the gauge through a hillslope linear reservoir and an advection–diffusion channel kernel from the linearized shallow-water equations. The parameters are found by calculating the posterior distribution from the MAP likelihood estimate, and each storm
is forecasted with a 90 % prediction interval before it is used in the update procedure.

Applied to Clear Creek near Coralville, IA (USGS 05454300) and the Rio Mora near Terrero, NM (USGS 08377900).

## Requirements

Python 3.10 or later.

```
pip install numpy scipy pandas matplotlib requests xarray cfgrib tifffile
```

cfgrib also needs the ecCodes library. On Windows the easiest route is
`conda install -c conda-forge cfgrib eccodes`.

## Files

| File | Contents |
|---|---|
| `distributed_iuh.py` | Basin geometry, radar download, precipitation losses, routing, plots |
| `sequential_bayes.py` | Priors, likelihood, Bayesian update, forecasting intervals |
| `nash_iuh.py` | Event extraction, baseflow and recession tools |
| `clear_creek_bayes.ipynb` | Full analysis for Clear Creek IA |
| `rio_mora_bayes.ipynb` | Full analysis for the Rio Mora NM |
| `likelihood_MAP_figure.ipynb` | Single-parameter illustration of updating procedure |

## Running

1. Open `clear_creek_bayes.ipynb` and run all cells.
2. The first run downloads the flow entire record, the basin geometry and the radar hours during each storm window, which can take up to a few hours. However, an interrupted download is able to resume later.
3. Later runs read the cached data, and you set `CACHED_ONLY = True` to skip all downloads.
4. The Bayesian run is saved, so re-running with the same storms and settings loads it
   instead of recomputing them all.

## Main settings (section 1 of each notebook)

| Setting | Meaning |
|---|---|
| `YEARS`, `MONTHS` | Period and season of the record |
| `MAX_EVENTS` | Number of largest storms kept |
| `N_CALIBRATE` | Storms used only for calibration (default 30) |
| `MIN_RISE_CFS` | Minimum peak rise for a storm; `None` = automatic |
| `CACHED_ONLY` | `True` = no downloads |
| `PRIOR` | Overrides of the default priors |

## Outputs

Everything is written to `data/USGS-<site>/`:
- `flow_iv.csv`, `mrms_basin.npz`, `nldi_*.geojson`: cached data
- `runs/`: saved Bayesian runs
- `report_figures/`: named figures

## Data sources

- Streamflow: USGS Water Data APIs
- Basin and streams: USGS Network Linked Data Index (NHDPlus V2)
- Rainfall: NOAA MRMS gauge-corrected QPE, via Iowa Environmental Mesonet and NOAA on AWS
- Elevation (map only): USGS 3DEP

## Author

Zach, 11/21/2026.