# Bvarta Bahari - Route & Fleet Optimization Challenge

**Author:** Fadilla Zundina  
**Position:** Data Scientist Candidate  

---

## 📌 Project Overview
This repository contains my submission for the Bvarta Bahari Data Scientist Challenge. The project focuses on solving complex maritime logistical challenges through data-driven approaches, including:
1. **Exploratory Data Analysis (EDA):** Establishing data quality and uncovering the critical Nautical Mile distance correction factor (1.2744).
2. **Predictive Modeling:** Developing a two-tiered *Integrated Risk-Adjusted Forecast* using LightGBM to predict daily ticket demand and mitigate weather-induced cancellation risks.
3. **Fleet Allocation & Optimization:** Implementing a Greedy Heuristic algorithm to restructure the assignment of 35 vessels across existing routes—respecting strict constraints like maximum operational hours (134 hrs/week) and minimum tide drafts—resulting in a projected profit increase of **Rp 15 Billion/month**.
4. **New Route Expansion:** Utilizing spatial mobility models to identify and project the financial feasibility (ROI) of 3 new maritime corridors.

---

## 📂 Repository Structure

```text
📦 bvarta_bahari_route_optimization
 ┣ 📂 data/                                  # Raw dataset files (CSV & GeoJSON) provided for the challenge
 ┣ 📂 output/                                # Generated final outputs
 ┃ ┣ 📜 summary_optimization.csv             # Final fleet allocation decisions for existing routes
 ┃ ┗ 📜 new_route_recommendations.csv        # Top 3 new route projections and recommended vessels
 ┣ 📂 visuals/                               # Client-ready presentation assets
 ┃ ┣ 📜 profit_comparison.png                # Financial impact bar chart (Before vs After)
 ┃ ┗ 📜 network_map.html                     # Interactive Folium map of optimized & expanded routes
 ┣ 📜 01_exploratory_data_analysis.ipynb     # Step 1: Data audit & spatial distance correction
 ┣ 📜 02_predictive_modeling.ipynb           # Step 2: Demand Regressor & Weather Classifier (LightGBM)
 ┣ 📜 03_route_optimization.ipynb            # Step 3: Fleet allocation algorithm & profit optimization
 ┣ 📜 04_new_route_expansion.ipynb           # Step 4: Spatial gravity modeling for network expansion
 ┣ 📜 TECHNICAL_WRITEUP.md                   # In-depth technical methodology, assumptions, and future works
 ┣ 📜 EXECUTIVE_PRESENTATION_STORYBOARD.md   # 10-Slide presentation storyboard tailored for C-Level
 ┗ 📜 README.md                              # This file
```

---

## 🚀 How to Run the Code

### 1. Prerequisites
Ensure you have Python 3.8+ installed. The codebase relies on standard data science libraries. You can install the required dependencies using:

```bash
pip install pandas numpy scikit-learn lightgbm matplotlib folium
```

### 2. Execution Order
The Jupyter Notebooks are designed to be run sequentially, as insights from earlier notebooks dictate assumptions in the latter ones.
1. Open and run `01_exploratory_data_analysis.ipynb`
2. Open and run `02_predictive_modeling.ipynb`
3. Open and run `03_route_optimization.ipynb` (This will generate `output/summary_optimization.csv`)
4. Open and run `04_new_route_expansion.ipynb` (This will generate `output/new_route_recommendations.csv`)

> **Note:** The notebooks contain rich, narrative Markdown explanations in Indonesian to walk you through the theoretical and business logic behind every line of code.

---

## 💡 Key Highlights & Business Impact
- **Projection Accuracy:** Integrated risk-adjustment reduced annual revenue projection error from +11.5% to just **-0.14%**.
- **Operational Safety:** Weather risk classifier achieved an ROC-AUC of **99.02%**, enabling proactive "No-Sail" decisions.
- **Financial Turnaround:** Restructuring the existing fleet transformed unprofitable routes into positive-margin operations, catapulting total monthly profit from **Rp 39.7 Billion** to **Rp 54.7 Billion**.
- **Strategic Expansion:** Successfully isolated 3 high-yield logistical corridors (e.g., Tanjung Priok - Tegal) projecting robust ROIs exceeding 400%.

For a deeper dive into the methodology and model validation metrics, please refer to the `TECHNICAL_WRITEUP.md`.
