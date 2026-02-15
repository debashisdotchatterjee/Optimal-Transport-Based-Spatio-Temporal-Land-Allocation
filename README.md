# Optimal-Transport-Based-Spatio-Temporal-Land-Allocation
# Optimal-Transport-Based-Spatio-Temporal-Land-Allocation

Reproducible Python pipeline for **Optimal Transport (OT)**–based land-use composition analysis and an **Operations Research (OR)** decision layer, using **World Bank World Development Indicators (WDI)**. The workflow downloads land indicators, constructs OT-ready compositions, computes OT distances and restoration plans toward a data-driven target, generates multi-objective rankings, and exports all tables/figures into a single zip.

Repository: https://github.com/debashisdotchatterjee/Optimal-Transport-Based-Spatio-Temporal-Land-Allocation

---

## 1) What this project does

Using WDI country-level indicators (2000–2023), the code:

1. **Downloads WDI series** for selected countries:
   - Agricultural land (% of land area): `AG.LND.AGRI.ZS`
   - Forest area (% of land area): `AG.LND.FRST.ZS`
   - Arable land (% of land area): `AG.LND.ARBL.ZS`

2. **Builds features**
   - Forest–agriculture contrast:  \(\Delta_{i,t}=F_{i,t}-A_{i,t}\)
   - Arable-within-agriculture proxy: \(\rho_{i,t}=R_{i,t}/A_{i,t}\)

3. **Constructs OT-ready compositions**
   - Since WDI categories overlap and do not sum to 100, the code maps
     \((A,F,R)\) to the simplex via:
     \[
     S_{i,t}=A_{i,t}+F_{i,t}+R_{i,t},\quad
     p^{(A)}_{i,t}=A_{i,t}/S_{i,t},\;
     p^{(F)}_{i,t}=F_{i,t}/S_{i,t},\;
     p^{(R)}_{i,t}=R_{i,t}/S_{i,t}.
     \]

4. **Computes OT geometry (2023 snapshot)**
   - Defines a category ground-cost matrix \(C\) (policy-weighted conversion difficulty)
   - Computes pairwise OT costs between countries’ compositions using **POT**.

5. **Defines a sustainable target & restoration plan (2023)**
   - Target composition \(q\) = median-normalized (across countries) in 2023
   - OT cost-to-target per country + recommended directional shifts:
     \[
     \text{shift}(i) = q - p_i.
     \]

6. **OR decision layer (multi-objective ranking)**
   - Combines: productivity proxy (arable %), sustainability proxy (forest %),
     and feasibility proxy (OT cost-to-target).
   - Composite score:
     \[
     \text{Score}(i)=w_P z(R_i) + w_S z(F_i) - w_C z(\text{OTcost}(i)).
     \]

7. **Produces decision visuals**
   - Scatter (forest vs agriculture, bubble = arable)
   - Correlation heatmap
   - OT distance matrix (CSV)
   - Bar chart of composite score
   - PCA map
   - Time-series and stacked compositions for selected countries
   - Productivity–sustainability frontier with bubble size = OT effort

8. **Exports all results**
   - Tables: `outputs/tables/*.csv`
   - Figures: `outputs/figures/*.png`
   - Zip bundle: `outputs/outputs.zip`

---
├── main.ipynb / main.py # main Colab-ready pipeline (your script)
├── outputs/
│ ├── tables/
│ │ ├── wdi_land_raw.csv
│ │ ├── coverage_by_country.csv
│ │ ├── wdi_land_features.csv
│ │ ├── snapshot_2023.csv
│ │ ├── corr_snapshot_2023.csv
│ │ ├── pairwise_ot_cost_2023.csv
│ │ ├── target_composition_2023.csv
│ │ ├── restoration_plan_ot_2023.csv
│ │ ├── ot_transport_plans_2023.csv
│ │ ├── multiobjective_score_2023.csv
│ │ ├── pca_snapshot_2023.csv
│ │ └── final_report_table_2023.csv
│ ├── figures/
│ │ ├── scatter_forest_vs_agri_2023.png
│ │ ├── corr_heatmap_2023.png
│ │ ├── bar_composite_score_top15_2023.png
│ │ ├── pca_snapshot_2023.png
│ │ ├── frontier_arable_vs_forest_bubble_otcost_2023.png
│ │ ├── timeseries_.png
│ │ └── stacked_comp_.png
│ └── outputs.zip
└── README.md


---

## 3) Requirements

The pipeline is tested for **Google Colab**.

Python packages:
- `pandas>=1.5`
- `numpy>=1.23`
- `matplotlib>=3.6`
- `scikit-learn>=1.2`
- `pandas-datareader>=0.10.0`
- `POT>=0.9.3`  (Python Optimal Transport)

In Colab, install quickly:

```bash
pip -q install pandas-datareader scikit-learn POT


## 2) Repository structure

