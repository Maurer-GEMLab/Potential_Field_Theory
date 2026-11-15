# Example datasets for the demos

## `semo_viburnum_mag.csv.gz`: airborne magnetics, Viburnum Trend, SE Missouri

A small subset of USGS Earth MRI airborne magnetic flight-line data, used by
[`../euler_deconvolution_demo.ipynb`](../euler_deconvolution_demo.ipynb).

**Source**

> McCafferty, A.E., and Johnson, M.R., 2019, Airborne magnetic and radiometric
> survey, southeast Missouri and western Illinois, 2018–2019: U.S. Geological
> Survey data release, https://doi.org/10.5066/P9PBDSA1.

The source file is `B478ARC_MAG191003.csv` (6.75 GB) from the *Magnetic data*
child item on ScienceBase:
https://www.sciencebase.gov/catalog/item/5dc5833ce4b06957975a47da.

USGS data are in the public domain. The USGS asks for acknowledgment in derived
products.

**Survey**

- Flown by Terraquest Ltd. for the USGS, December 2018 – May 2019.
- Fixed-wing aircraft with three Scintrex CS-3 cesium magnetometers: tail stinger
  plus both wing tips (horizontal gradiometer).
- North–south traverse lines 300 m apart; east–west tie lines every 3000 m.
- Draped flight surface, 117 m mean terrain clearance.
- Sampled at 10 Hz, about 7 m along line.
- IGRF in the study area: inclination ≈ 65.8°, declination ≈ −0.8°,
  F ≈ 51,510 nT.

**Subset**

| | |
|---|---|
| Area | UTM zone 15N (WGS 84) E 642,000–672,000 m, N 4,145,000–4,195,000 m (≈ 37.44–37.89° N, 91.39–91.04° W): a 30 km × 50 km block centred on Viburnum, MO |
| Lines | north–south traverse lines (`L*`) only; tie lines removed |
| Rows | 100,035 observations on 111 lines |
| Sampling | decimated along line to ~50 m |
| Size | 2.7 MB (gzip) |

Processing steps:

1. Stream the full CSV and keep rows inside a lat/lon box around the area.
2. Strip the Windows `\r` line endings.
3. Keep traverse lines only. Drop rows with missing coordinates or magnetic
   channels, or without a `X_UTM_TRIM` value (the contractor's flag for line
   overlap).
4. Clip to the UTM box above.
5. Within each line, sorted by fiducial, keep the first sample at or beyond each
   50 m step of cumulative along-line distance.
6. Round the values for compactness.

No other filtering, levelling or gridding was applied.

**Columns**

| Column | Source channel | Units | Description |
|---|---|---|---|
| `LINE` | `LINE` | — | flight line ID |
| `FID` | `FID` | s | fiducial (UTC seconds of day) |
| `X`, `Y` | `X_UTM_WIN`, `Y_UTM_WIN` | m | UTM 15N easting / northing (WGS 84) |
| `ALT` | `ALT` | m a.s.l. | GPS altitude of the aircraft |
| `RADAR` | `RADAR` | m | radar-altimeter terrain clearance |
| `DTM` | `DTMFNL` | m a.s.l. | final digital terrain model |
| `TMI` | `TF3FNL` | nT | final micro-levelled total magnetic intensity (tail sensor) |
| `TMI_ANOM` | `TF3ANM` | nT | `TMI` minus the IGRF (total-field anomaly) |
| `HG_EW` | `HGEWFNL` | nT/m | measured east–west horizontal gradient |
| `HG_NS` | `HGNSFNL` | nT/m | measured north–south horizontal gradient |

Read it with:

```python
import pandas as pd
df = pd.read_csv('demos/data/semo_viburnum_mag.csv.gz')
```
