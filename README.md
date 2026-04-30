# AID843 – Spatio-Temporal Data Analytics-I  
## Assignments 1, 2 & 3: Soccer Match-Event Analysis

**Course:** AID843 Spatio-Temporal Data Analytics-I, Term 2 (2025-26)  
**Institute:** IIIT Bangalore

---

## Team

| Name | Roll No. |
|------|----------|
| R. Sreenivasa Raju | IMT2023122 |
| U. Trivedh Venkata Sai | IMT2023002 |
| A. Rajdeep | IMT2023592 |

---

## Dataset

**Source:** [Soccer Match Event Dataset – Kaggle](https://www.kaggle.com/datasets/aleespinosa/soccer-match-event-dataset)

The dataset contains on-pitch event records (passes, shots, duels, fouls, free kicks, etc.) from five top European football leagues and two international tournaments. Spatial coordinates are normalised to a 0–100 scale on both axes (percentage of pitch length/width). Each event is linked to a match via `matchId`/`wyId`.

**CSV files required** (download from Kaggle and place in the repo root):

```
events_England.csv            matches_England.csv
events_Spain.csv              matches_Spain.csv
events_Italy.csv              matches_Italy.csv
events_Germany.csv            matches_Germany.csv
events_France.csv             matches_France.csv
events_World_Cup.csv          matches_World_Cup.csv
events_European_Championship.csv  matches_European_Championship.csv
```

---

## Repository Structure

```
.
├── Assignment1/
│   ├── A1_England.ipynb              # England spatial analysis pipeline
│   ├── A1_Top4_Leagues.ipynb         # Cross-league spatial comparison (Spain, Italy, Germany, France)
│   └── Assignment1_Report.pdf        # Final report (IEEE format)
├── Assignment2/
│   ├── a2_topfive_leagues.ipynb      # Temporal analysis – 5 leagues + World Cup
│   ├── a2_tournaments.ipynb          # Temporal analysis – World Cup vs Euro 2016
│   └── Assignment2_report.pdf        # Final report
├── Assignment3/
│   ├── Assignment3.ipynb             # Spatio-Temporal ML & Data Mining
│   └── Assignment3_Report.pdf        # Final report
└── README.md
```

---

## Assignment 1 – Spatial Analysis of Soccer Match Events

**Notebook(s):** `A1_England.ipynb`, `A1_Top4_Leagues.ipynb`

### Overview
Exploratory spatial data analysis on the full event dataset, using shot events as the primary spatial variable for cross-league comparison, and all event types for the England deep-dive.

### Pipeline

**Task 1 – Preprocessing**
- Column selection, coordinate clipping (0–100), missing value removal
- Grid zone assignment: 10×10 for England, 11×11 for cross-league
- GeoDataFrame construction using cell centroids

**Task 2 – Spatial Autocorrelation**
- Global: Moran's I, Geary's C, Getis-Ord G
- Local: LISA cluster maps, Getis-Ord G* hotspot maps
- Stratified analysis by event type for England

**Task 3 – Spatial Heterogeneity**
- Coefficient of variation across pitch zones
- Levene's test and one-way ANOVA across pitch thirds (defensive / midfield / attacking)
- Cross-league comparison of heterogeneity metrics

**Task 4 – Spatial Regression**
- OLS baseline, Spatial Lag (GM_Lag), Spatial Error (GM_Error)
- 80/20 holdout validation with MAE reporting
- Residual Moran's I diagnostics
- Geographically Weighted Regression (GWR) case study on Spain

All leagues show statistically significant positive spatial autocorrelation in shot placement. Italy and Spain exhibit the strongest clustering, consistent with their structured positional playing styles.

---

## Assignment 2 – Temporal Analysis of Soccer Match Events

**Notebook(s):** `a2_topfive_leagues.ipynb`, `a2_tournaments.ipynb`

### Overview
Two parallel temporal studies:
1. **`a2_topfive_leagues.ipynb`** – Season-long temporal analysis across the 5 European leagues + World Cup 2018, using gameweek-indexed time series.
2. **`a2_tournaments.ipynb`** – Tournament-level comparison between **World Cup 2018** and **UEFA Euro 2016**, using match-indexed time series (more granular than round-level).

### Data Construction
Events carry no dates; dates live in the match files. Both notebooks join events → matches on `matchId`/`wyId` to obtain `dateutc` and `gameweek`, then build:
- **Gameweek series** – shots/events aggregated per gameweek (leagues)
- **Match-level series** – one row per match: shots, passes, duels, free kicks, total goals (tournaments)
- **Within-match series** – events binned into 5-minute intervals using `eventSec`

### Pipeline

**Task 1 – Preprocessing & Decomposition**
- Rolling means (7-match window) for trend smoothing
- Seasonal decomposition (additive & multiplicative, `period=5` for leagues; `period=8` for tournaments)
- Detrending (subtract trend component), deseasonalization (subtract seasonal component)
- Differencing: 1st and 2nd order to achieve stationarity

**Task 2 – Temporal Autocorrelation & Stationarity**
- ACF / PACF plots on original and first-differenced series
- Lag plots (lags 1, 2, 4/5, 8/10)
- ADF (Augmented Dickey-Fuller) test – H₀: non-stationary
- KPSS test – H₀: stationary (complementary to ADF)
- Cross-league stationarity comparison table

**Task 3 – Temporal Regression & Forecasting**
| Model | Description |
|-------|-------------|
| Simple Linear Regression | Gameweek index as regressor |
| Polynomial Regression (deg=3) | Captures non-linear trend |
| Multi-feature Linear Regression | Passes + duels + goals as features |
| ARIMA(1,1,1) | Baseline |
| ARIMA(2,1,2) | Higher-order |
| ARIMA(1,0,1) | On stationary series |
| SARIMA(1,1,1)(1,0,1,5/8) | Seasonal ARIMA |

All models trained on 80% holdout, evaluated with MAE and RMSE on the remaining 20%.

**Task 4 – Model Comparison & Residual Diagnostics**
- MAE / RMSE / AIC comparison tables
- Residual time plots, histograms, ACF of residuals, Q-Q plots

### Key Findings (Leagues)
- All six series (five leagues + World Cup) are **stationary at origin** per ADF test (p < 0.05), eliminating the need for differencing before ARIMA.
- ARIMA orders selected automatically vary by league; SARIMA provides modest improvement over plain ARIMA for England.
- Multi-feature linear regression (using passes + duels + goals as predictors) outperforms simple time-index regression on the match-level series.

### Key Findings (Tournaments)
- **World Cup 2018** (64 matches): avg ~26 shots/match, avg ~2.64 goals/match, avg ~430 passes/match.
- **UEFA Euro 2016** (51 matches): avg ~25 shots/match, avg ~2.12 goals/match, avg ~445 passes/match.
- Both series are non-stationary (ADF p > 0.05); first differencing achieves stationarity.
- Shot intensity increases in knockout rounds for both tournaments, visible from the match-level timeline.
- Multi-feature LR consistently achieves lower MAE than pure time-index regression for both tournaments.

---

## Assignment 3 – Spatio-Temporal ML & Data Mining

**Notebook:** `Assignment3.ipynb`

### Overview
Five integrated tasks combining spatial ML, temporal ML, spatial data mining, temporal data mining, and joint spatio-temporal inference across all five leagues + both tournaments.

### Feature Engineering

**Spatial features** (per 10×10 grid zone, per league):
`x_coord`, `y_coord`, `dist_to_goal`, `is_central`, `pass_count`, `duel_count`, `foul_count`, `shot_rate`, `spatial_lag_shots`

**Temporal / match-level features:**
`shots`, `passes`, `duels`, `fouls`, `freekicks`, `shots_lag1`, `goals_lag1`, `shots_roll3`, `goals_roll3`, `half_ratio`, `shot_spread_x`, `shot_spread_y`, `shot_avg_x`

---

### Task 1 – Spatial ML

**5A – Spatial Regression (England, tuned)**  
Target: `shot_count` per grid zone. Models tuned via GridSearchCV / RandomizedSearchCV (3-fold CV):
- Random Forest (RF) Regressor
- KNN Regressor
- Ridge Regression
- SVR (with RBF/linear kernel)

Outputs: Feature importance bar chart, R² by model, tuned RF predicted shot-density heatmap, predicted vs actual scatter plots.

**5B – Hotspot Classification (pooled leagues)**  
Binary target: `is_hotspot` (derived from LISA). Tuned models: RF, KNN, SVM, Logistic Regression. Stratified 80/20 split, class-weighted for imbalance. Evaluated by Accuracy + weighted F1.

**5C – League Multi-class Classification**  
5-class target: league identity from spatial features. Tuned RF with `RandomizedSearchCV`; outputs classification report per league.

---

### Task 2 – Temporal ML

**6A – Gameweek Regression with STL + auto_arima**  
- STL decomposition (robust=True, period=5) on England shots-per-gameweek; reports Trend strength (Fₜ) and Seasonal strength (Fₛ).
- `pmdarima.auto_arima` selects optimal ARIMA order per league (AIC-based, stepwise).
- ML regressors on lag/rolling features (`lag1`–`lag5`, `roll3`, `roll5`, `passes`, `duels`, `goals`): tuned RF and Ridge.

**6B – Match Result Classification**  
3-class target: Home Win / Draw / Away Win. Tuned models: RF, Gradient Boosting (GBT), SVM, KNN. Evaluated by Accuracy + weighted F1 + confusion matrix.

---

### Task 3 – Spatial Data Mining

**7A/B – K-Means Clustering**  
Elbow method on 10,000 sampled England events; optimal K=6. Cluster profiles by event type (shots, passes, duels, etc.).

**7C – DBSCAN on Shot Locations**  
k-distance graph to pick `eps`; DBSCAN run on standardised shot coordinates. Reports cluster count and noise fraction.

**7D – Collocation Analysis**  
Zone-level co-occurrence measured by:
- Pearson correlation (baseline)
- Collocation Quotient CQ(A,B) = P(A∩B)/(P(A)·P(B)) — values > 1 indicate co-location beyond chance
- Jaccard similarity |A∩B|/|A∪B|
Cross-league Shot–Pass CQ comparison included.

**7E – Spatio-Temporal K-Means**  
K-Means on 3D feature space (x, y, match_minute) to identify spatial zones that are active at different phases of the match. Cluster centres decoded to pitch zone (Defensive/Midfield/Attacking) and match half; visualised as 2D scatter (minute colour-coded) and 3D scatter.

---

### Task 4 – Temporal Data Mining

**8A – Pre-Shot Sequence Analysis**  
Extracts 5-event windows immediately before each shot; reports most frequent events at positions −1 and −2 before the shot.

**8B – PrefixSpan Formal Sequential Pattern Mining**  
Encodes event types as integers; runs PrefixSpan with min_support = max(5, 5% of sequences). Reports top multi-step patterns. Also includes Apriori association rules on event-sequence transactions. Cross-competition comparison (England, World Cup, Euro 2016).

**8C – Team Shot-State Transition Matrix**  
Per match, each team is labelled High-Shot or Low-Shot (above/below median). Bigrams across consecutive matches build a 2×2 Markov transition probability matrix showing persistence of shot state.

**8D – Gameweek Shot-Level Patterns**  
Shot counts per gameweek quantile-binned into Low/Medium/High; bigram and trigram frequencies extracted; 3×3 transition matrix visualised.

**8E – Zone × Time-bin Density Matrix**  
Events binned by pitch zone (Defensive/Midfield/Attacking) and 15-minute time window. Separate heatmaps for all events and shots-only reveal how spatial dominance shifts across match phases.

---

### Task 5 – Joint Spatio-Temporal Inference

**9A – Joint Feature Table**  
Per-match spatial features (`shot_atk_ratio`, `shot_dist_goal`) merged with temporal match features to form the joint dataset.

**9B – PCA (League-level)**  
13 aggregated spatial + temporal features per league normalised and projected to PC1/PC2. Spatial per-match features (`shot_avg_x`, `shot_atk_ratio`, `shot_dist_goal`) included (fixes earlier version that used only zone-level spatial stats).

**9C – Joint ML with Ablation Study**  
5-class league classification with RF, evaluated across three feature sets:

| Feature Set | Description |
|-------------|-------------|
| Temporal only | `shots`, `passes`, `duels`, lag & rolling features, `half_ratio` |
| Spatial only | `shot_avg_x`, `shot_spread_x/y`, `shot_atk_ratio`, `shot_dist_goal` |
| Joint (both) | All of the above combined |

The ablation conclusively shows Joint > Spatial-only ≈ Temporal-only, justifying the combined spatio-temporal approach. Additional full joint models: SVM + KNN with tuning.

---

## Requirements

```
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
statsmodels
pmdarima
mlxtend
prefixspan
```

Install with:

```bash
pip install pandas geopandas numpy matplotlib seaborn scipy libpysal esda spreg mgwr scikit-learn shapely statsmodels pmdarima mlxtend prefixspan
```

---

## Reports

All three reports follow IEEE two-column conference format.

| Assignment | Report File |
|------------|-------------|
| Assignment 1 | `Assignment1/Assignment1_Report.pdf` |
| Assignment 2 | `Assignment2/Assignment2_report.pdf` |
| Assignment 3 | `Assignment3/Assignment3_Report.pdf` |
