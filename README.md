# Spatiotemporal Drought Characterisation and Exposure Mapping Using MODIS VCI/TCI/VHI and CHIRPS Rainfall, Tharparkar, 2001–2024

Author: Malaika Noor

Final year project, BS (Hons.) Geo-Informatics (GIS and Remote Sensing). All analysis runs in one Google Colab notebook (`tharparkar_drought_analysis.ipynb`) using Google Earth Engine.

## Summary

Monsoon (July–October) drought in Tharparkar District is characterised for 2001–2024 using CHIRPS rainfall and MODIS vegetation and temperature indices. Three seasons are identified as dry by the Standardized Precipitation Index (2002, 2004, 2018). Their vegetation stress is mapped with the Vegetation Condition Index, and the 2020 WorldPop population living in each drought class is calculated as a measure of exposure. The main contribution is the characterisation of the dry seasons and their vegetation stress; the trend results are secondary and are reported with their limits.

## Repository contents

- `tharparkar_drought_analysis.ipynb`: the Google Colab notebook with all code (Cells 1–8)
- `outputs/`: maps, figures, tables and GeoTIFF layers produced by the notebook (listed in the Outputs section below)
- `README.md`: this file

## How to run

1. Open `tharparkar_drought_analysis.ipynb` in Google Colab.
2. In Cell 1, replace `YOUR-GEE-PROJECT-ID` with your own Google Earth Engine project ID.
3. Run the cells in order. Cell 1 asks you to sign in to Earth Engine. Later cells use the results of earlier ones, so do not skip any.

## Study area

Tharparkar District, Sindh, Pakistan (about 19,600 km²). A hot, arid district that depends on the July–October monsoon for its rainfall and vegetation.

## Data

| Data | Source | Resolution |
|---|---|---|
| Rainfall | CHIRPS v2.0 daily (UCSB-CHG) | about 5.5 km |
| Vegetation (NDVI) | MODIS MOD13A2 v061 (NASA LP DAAC) | 1 km |
| Land surface temperature | MODIS MOD11A2 v061 (NASA LP DAAC) | 1 km |
| Water mask | JRC Global Surface Water (occurrence ≥ 25 %) | 30 m, applied to 1 km layers |
| Population | WorldPop 2020 (100 m) | 100 m, summed to the 1 km grid |
| Census totals | Pakistan Bureau of Statistics, 2017 and 2023 | district level |
| District boundary | FAO GAUL 2015 | vector |

Study period: 2001–2024 (288 months). Coordinate system of all maps: WGS 84 (EPSG:4326).

## Method

1. Vegetation Condition Index (VCI), Temperature Condition Index (TCI) and Vegetation Health Index (VHI) are computed from MODIS NDVI and land surface temperature following Kogan (1995) and averaged over the district to give a monthly series of 288 months (Cell 2). VCI scales NDVI between its minimum and maximum, TCI scales land surface temperature in the opposite direction between its maximum and minimum, and VHI is the equal-weight average of VCI and TCI.
2. Yearly and July–October summaries are built, together with CHIRPS rainfall (annual and July–October totals) (Cell 3).
3. VCI drought classes (extreme <10, severe 10–20, moderate 20–30, mild 30–40, none ≥40), following the scheme commonly attributed to Kogan (1995).
4. The Standardized Precipitation Index (SPI) of the July–October season is calculated by fitting a gamma distribution to the 2001–2024 seasonal totals (McKee et al., 1993). Seasons with SPI ≤ −1 are treated as dry seasons.
5. TCI and VHI from 2020 onward are flagged as unreliable because of Terra and Aqua orbit drift, and are excluded from the correlation and trend checks that use the reliable period (2001–2019).
6. Map layers are built in Earth Engine and downloaded as GeoTIFFs (Cell 4): mean rainfall, mean NDVI, frequency of rainfall below 75 % of the pixel mean, rainfall as a percentage of average, and VCI for each dry season. Maps are drawn in Cell 5; pixels outside the district are removed and pixels with value ≤ 0 in the rainfall and NDVI means are set to no data (the counts are printed by the cell). Class breaks for the mean rainfall and mean NDVI maps hold roughly equal numbers of pixels.
7. Exposure (Cells 6 and 7): WorldPop 2020 population is summed onto the 1 km VCI grid, and the share of the district population in each VCI class is calculated for the dry seasons. Population is also scaled to a 2020 census estimate interpolated between the 2017 and 2023 censuses. The summed grid total is 1,285,219, which is 0.996 of the Earth Engine total of 1,290,684, so no population was lost in the summing step.
8. Trends are tested with the Mann–Kendall test and the Sen slope on the yearly series, also after removing 2002, and on the VCI residual after removing the effect of rainfall (Cell 8, `trend_tests.csv`).

## Results

### Rainfall and SPI

District-mean July–October rainfall averages about 286 mm per season and ranges from 23 mm (2002) to 522 mm (2011). Mean rainfall rises from west to east across the district (below 249 mm in the west to 318 mm or more in the east, Map 1).

Three seasons have SPI ≤ −1:

| Season | Jul–Oct rainfall (mm) | SPI | Jul–Oct VCI | Jul–Oct VHI |
|---|---|---|---|---|
| 2002 | 23.3 | −3.04 | 6.65 | 14.81 |
| 2004 | 104.3 | −1.38 | 14.35 | 30.49 |
| 2018 | 98.5 | −1.45 | 16.77 | 19.34 |

- 2002 was a district-wide drought: rainfall was below 10 % of average in the west and 10–25 % elsewhere, and most of the district had extreme vegetation stress (Maps 4 and 5).
- 2004 and 2018 were milder: rainfall was mostly 25–50 % of average and the vegetation response was patchy.
- Mean monsoon NDVI is low and fairly uniform (median 0.243; most pixels between 0.22 and 0.27), with the lowest values in the north (Map 2).

### Does vegetation follow rainfall?

July–October VCI is strongly correlated with SPI (2001–2024: VCI 0.74, TCI 0.63, VHI 0.73; 2001–2019: VCI 0.77, TCI 0.71, VHI 0.81). VCI and rainfall are correlated at 0.69, with about 9.5 VCI units per 100 mm.

### Exposure

Share of the district population (WorldPop 2020) living in extreme or severe VCI zones (VCI < 20):

| Season | Extreme (<10) | Severe (10–20) | Extreme + severe | People (WorldPop) | People (scaled to census) |
|---|---|---|---|---|---|
| 2002 | 82.6 % | 12.9 % | 95.5 % | 1,227,101 | 1,640,351 |
| 2004 | 41.2 % | 40.8 % | 82.0 % | 1,053,605 | 1,408,428 |
| 2018 | 38.5 % | 29.7 % | 68.3 % | 877,690 | 1,173,269 |

Vegetation stress covered most of the district in all three seasons, so the combined shares are high. The extreme class alone differs more between seasons (82.6 % in 2002, 41.2 % in 2004, 38.5 % in 2018), and the density map (`map_06_population_exposure.png`) shows where the exposed population is concentrated.

About 2 % of the population falls in masked water or no-data pixels in each season. The full table is in `exposure_population_by_vci_class.csv`.

### Trends (2001–2024, n = 24)

| Series | Kendall tau | p-value | Sen slope per decade | 95 % CI per decade |
|---|---|---|---|---|
| July–October rainfall (mm) | 0.30 | 0.044 | 87.07 | 2.36 to 155.51 |
| July–October SPI | 0.30 | 0.044 | 0.61 | 0.01 to 1.10 |
| July–October VCI | 0.35 | 0.017 | 13.69 | 2.15 to 26.07 |
| July–October VHI (2001–2019, n = 19) | 0.13 | 0.447 | 1.93 | −7.04 to 16.12 |
| Rainfall without 2002 (n = 23) | 0.24 | 0.114 | 63.05 | −21.34 to 138.75 |
| VCI without 2002 (n = 23) | 0.30 | 0.050 | 12.34 | −0.32 to 22.82 |
| VCI residual after removing rainfall | 0.22 | 0.145 | 7.82 | −2.10 to 15.67 |

The upward trends in rainfall and VCI are weak. They depend on the extreme 2002 drought at the start of the record and on the wet seasons of 2020–2024: without 2002 the rainfall trend is not significant and the VCI trend is borderline, with a confidence interval that includes zero. After the effect of rainfall is removed, the VCI trend is not significant (p = 0.145), so most of the VCI increase is explained by the rainfall pattern. They are described as a tendency, not as established greening or wetting.

## Validation against documented droughts

- The multi-year drought of about 1998–2002 is described as the most severe in recent decades, and 2014 as the most recent major drought (Siddiqui and Safi, 2018). The Sindh Relief Department has declared Tharparkar drought-affected in 2001, 2004, 2005, 2007, 2012–2015, 2017–2020 and several earlier years, as listed in secondary sources (Wikipedia; CanSFE).
- The three SPI-dry seasons (2002, 2004, 2018) are all documented drought years.
- The five seasons with the lowest July–October VCI are 2002 (6.65), 2004 (14.35), 2018 (16.77), 2014 (18.69) and 2005 (20.03). All five are documented drought years. 2012 and 2015, also declared drought years, rank 8th and 7th lowest.
- 2014 has an SPI of only −0.49 (rainfall 192 mm), so the SPI ≤ −1 rule misses it, while VCI, TCI and VHI are all low in that season (VCI 18.69, TCI 18.79, VHI 18.58). A drought declaration depends on water supply and livelihood impacts as well as the July–October rainfall total, so the vegetation indices and the rainfall index can differ.
- The comparison is with declared drought years and published descriptions only. No field or crop-yield data were used.

## Outputs

| Cell | Purpose | Main files in `outputs/` |
|---|---|---|
| 1 | Setup and study area | none |
| 2 | Monthly VCI, TCI, VHI | `vhi_monthly_timeseries.csv`, `fig_vhi_timeseries.png` |
| 3 | Yearly summary, CHIRPS, SPI | `yearly_summary_with_SPI.csv`, `fig_diagnostics.png` |
| 4 | Map layers from Earth Engine | `thar_*.tif` |
| 5 | Maps 1–5 | `map_01_mean_rainfall.png` to `map_05_vci_dry_seasons.png` |
| 6 | Population exposure table and chart | `exposure_population_by_vci_class.csv`, `exposure_population_chart.png` |
| 7 | Exposure map | `map_06_population_exposure.png` |
| 8 | Trend tests | `trend_tests.csv`, `fig_trends.png` |

## Limitations

- TCI and VHI from 2020 onward are treated as unreliable because of Terra and Aqua orbit drift. They are kept in the files and flagged.
- Only three seasons are SPI-dry, and the SPI is based on 24 seasons, shorter than the 30 years usually recommended for a stable baseline.
- The trend tests use 24 values, do not correct for autocorrelation, and are sensitive to 2002, as shown in the trend table.
- CHIRPS pixels are about 5.5 km, so the rainfall maps are coarse and some patterns (for example the north block on the deficit map) cover only a few cells. The deficit-frequency classes differ by only 1–3 seasons.
- Mean NDVI varies little across the district, so differences between its map classes are small (about 0.01–0.02).
- VCI compares each pixel with its own minimum and maximum. A small greening in a very sparse area (such as the north patch in 2018) can give a high VCI without much vegetation, so it is not interpreted as irrigated land. The southern coastal strip (Rann of Kutch salt flats) is unreliable for NDVI.
- Exposure uses a fixed 2020 population against past droughts. It is not the historical population of 2002, 2004 or 2018. WorldPop is a modelled estimate and its district total (1.29 million) is about 25 % below the census-based estimate for 2020 (about 1.7 million), so both counts are given. Because vegetation stress covered most of the district in all three dry seasons, the exposure shares are high and show little contrast between areas.
- Explanations for the patchy vegetation response in 2004 and 2018 are hypotheses and were not tested.
- The drought-year records used for validation come partly from secondary sources.

## References

- Kogan, F. N. (1995). Application of vegetation index and brightness temperature for drought detection. Advances in Space Research, 15(11), 91–100. https://doi.org/10.1016/0273-1177(95)00079-T
- McKee, T. B., Doesken, N. J., & Kleist, J. (1993). The relationship of drought frequency and duration to time scales. Preprints, 8th Conference on Applied Climatology, 17–22 January 1993, Anaheim, California, American Meteorological Society, 179–184.
- Funk, C., Peterson, P., Landsfeld, M., Pedreros, D., Verdin, J., Shukla, S., Husak, G., Rowland, J., Harrison, L., Hoell, A., & Michaelsen, J. (2015). The climate hazards infrared precipitation with stations—a new environmental record for monitoring extremes. Scientific Data, 2, 150066. https://doi.org/10.1038/sdata.2015.66
- Pekel, J.-F., Cottam, A., Gorelick, N., & Belward, A. S. (2016). High-resolution mapping of global surface water and its long-term changes. Nature, 540, 418–422. https://doi.org/10.1038/nature20584
- Wan, Z., Hook, S., & Hulley, G. (2021). MODIS/Terra Land Surface Temperature/Emissivity 8-Day L3 Global 1km SIN Grid V061. NASA Land Processes DAAC. https://doi.org/10.5067/MODIS/MOD11A2.061
- MODIS/Terra Vegetation Indices 16-Day L3 Global 1km SIN Grid V061 (MOD13A2). NASA EOSDIS Land Processes DAAC. https://doi.org/10.5067/MODIS/MOD13A2.061
- WorldPop (www.worldpop.org – School of Geography and Environmental Science, University of Southampton; Department of Geography and Geosciences, University of Louisville; Département de Géographie, Université de Namur) and Center for International Earth Science Information Network (CIESIN), Columbia University (2018). Global High Resolution Population Denominators Project, funded by the Bill and Melinda Gates Foundation. https://doi.org/10.5258/SOTON/WP00645
- Pakistan Bureau of Statistics. Population Census 2017, Tharparkar District (Table 1). Census 2023 total as tabulated by citypopulation.de from Pakistan Bureau of Statistics data.
- Siddiqui, S., & Safi, M. W. A. (2018). Assessing the socio-economic and environmental impacts of 2014 drought in District Tharparkar, Sindh-Pakistan. International Journal of Economic and Environment Geology, 8(4), 8–15. https://doi.org/10.46660/ijeeg.v8i4.723
