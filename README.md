# AID843 - Assignment 1: Spatial Analysis of Soccer Match Events

**Course:** AID843 Spatio-Temporal Data Analytics-I, Term 2 (2025-26)  
**Institute:** IIIT Bangalore  



## Overview

This repository contains the code and report for Assignment 1 of AID843. The assignment performs exploratory spatial data analysis on a large-scale soccer match event dataset covering five top European football leagues.

The dataset is sourced from: https://www.kaggle.com/datasets/aleespinosa/soccer-match-event-dataset


## Team

- [R.Sreenivasa Raju] — IMT2023122
- [U.Trivedh Venkata Sai] — IMT2023002
- [A.Rajdeep] — IMT2023592


## Repository Structure


.
├── A1_England.ipynb           # Full spatial analysis pipeline for England
├── A1_Top4_Leagues.ipynb      # Cross-league analysis (Spain, Italy, Germany, France)
├── report
└── README.md


## Dataset

The Soccer Match Event Dataset contains on-pitch event records (passes, shots, duels, fouls, etc.) from five European leagues. Spatial coordinates are normalised to a 0-100 scale on both axes, representing percentage of pitch length and width.

The analysis focuses on **shot events** as the primary spatial variable for comparision of top four leagues and analyzed all on-pitch event records for England League(Premier League) . The dataset files required to run the notebooks are:

- events_England.csv
- events_Spain.csv
- events_Italy.csv
- events_Germany.csv
- events_France.csv

These files are not included in the repository due to size. Download them from the Kaggle link above and place them in the root directory before running the notebooks.

## Analysis Summary

**Task 1 - Preprocessing**
- Column selection, coordinate clipping, missing value removal
- Grid zone assignment (10x10 for England, 11x11 for cross-league)
- GeoDataFrame construction using cell centroids

**Task 2 - Spatial Autocorrelation**
- Global: Moran's I, Geary's C, Getis-Ord G
- Local: LISA cluster maps, Getis-Ord G* hotspot maps
- Stratified analysis by event type for England

**Task 3 - Spatial Heterogeneity**
- Coefficient of variation across pitch zones
- Levene's test and one-way ANOVA across pitch thirds
- Cross-league comparison of heterogeneity metrics

**Task 4 - Spatial Regression**
- OLS baseline, Spatial Lag (GM_Lag), Spatial Error (GM_Error)
- 80/20 holdout validation with MAE reporting
- Residual Moran's I diagnostics
- Geographically Weighted Regression (GWR) case study on Spain


## Key Results

| League  | Moran's I | p-value | GWR R2 |
|---------|-----------|---------|--------|
| England | 0.116     | 0.015   | -      |
| Spain   | 0.325     | 0.001   | 0.414  |
| Italy   | 0.391     | 0.001   | -      |
| Germany | 0.195     | 0.002   | -      |
| France  | 0.125     | 0.025   | -      |

All leagues show statistically significant positive spatial autocorrelation in shot placement. Italy and Spain exhibit the strongest clustering, consistent with their structured positional playing styles.



## Requirements

pandas
geopandas
numpy
matplotlib
seaborn
scipy
libpysal
esda
spreg
mgwr
scikit-learn
shapely


Install with:

```
pip install pandas geopandas numpy matplotlib seaborn scipy libpysal esda spreg mgwr scikit-learn shapely
```

---

## Report

The report follows IEEE two-column conference format and is written in LaTeX. It covers dataset description, spatial analysis techniques, experiments, results, discussion, and conclusion.

To compile:

```
pdflatex soccer_spatial_report.tex
pdflatex soccer_spatial_report.tex
```

Run twice to resolve cross-references.
