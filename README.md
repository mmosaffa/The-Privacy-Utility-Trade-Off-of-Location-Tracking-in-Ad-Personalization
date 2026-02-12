# The-Privacy-Utility-Trade-Off-of-Location-Tracking-in-Ad-Personalization
This researchstudies whether geographical and behavioral data function as complements or substitutes in achieving business objectives, emphasizing the value–privacy trade-off. Geography adds value when behavioral histories are sparse, but becomes a weak substitute as histories grow rich, offering limited incremental gains relative to privacy risk.

### Data, Models, and Policy Evaluation Pipeline

This repository contains the full empirical pipeline for the research project studying **when and how geographic information adds value relative to behavioral data in ad personalization**. The code implements a modular, end-to-end workflow that moves from raw impression logs to counterfactual policy evaluation, mechanism tests, and final empirical results.

The project is designed for **transparency, reproducibility, and clear identification**, with each stage isolated in a dedicated notebook.

---

## Project Structure

The repository consists of **six consecutive notebooks**, each corresponding to a distinct conceptual step in the analysis:

├── 1_GitHub_Data_Prepration.ipynb

├── 2_GitHub_LSTM_Training.ipynb

├── 3_GitHub_Propensity_Score.ipynb

├── 4_GitHub_Xgboost.ipynb

├── 5_GitHub_RSA_Mechanism.ipynb

└── 6_GitHub_Empirical_Results.ipynb

---

## File-by-File Description

### **1. Data Preparation & Feature Generation**
**`1_GitHub_Data_Prepration.ipynb`**

This notebook prepares the raw impression-level data for all downstream analyses.

Main tasks:
- Cleans and standardizes raw logs (IDs, timestamps, locations).
- Constructs **within-user chronological impression histories**.
- Applies sample restrictions (e.g., first 150 impressions per user).
- Generates feature sets corresponding to different information regimes:
  - Contextual
  - Geographical
  - Behavioral
  - Geographical + Behavioral
- Encodes categorical variables and exports train/test datasets.

**Output:** Model-ready datasets used by all subsequent notebooks.

---

### **2. Sequential Behavioral Modeling**
**`2_GitHub_LSTM_Training.ipynb`**

This notebook estimates the **behavioral signal** using sequence models.

Main tasks:
- Constructs ordered impression sequences at the user level.
- Trains an **LSTM** to predict click probability based solely on past behavior.
- Captures dynamic engagement patterns such as learning, persistence, and habit formation.
- Produces out-of-sample behavioral predictions.

**Output:** Behavioral click predictions used for counterfactual analysis and mechanism tests.

---

### **3. Propensity Score Estimation**
**`3_GitHub_Propensity_Score.ipynb`**

This notebook models the **logging policy** that generated observed ad assignments.

Main tasks:
- Estimates ad-level propensity scores \( \hat\pi_D(a \mid i) \).
- Defines the support of valid counterfactual evaluation.
- Provides reweighting terms required for inverse propensity scoring (IPS).

**Output:** Propensity scores merged into policy evaluation datasets.

---

### **4. Counterfactual Prediction & Policy Construction**
**`4_GitHub_Xgboost.ipynb`**

This notebook generates **counterfactual outcomes** under alternative information regimes.

Main tasks:
- Trains XGBoost models using:
  - Contextual features
  - Geographic features
  - Behavioral predictions
  - Combined geographic + behavioral information
- Predicts counterfactual click probabilities for each ad.
- Constructs candidate policies for evaluation.

**Output:** Counterfactual prediction tables for policy value estimation.

---

### **5. Mechanism Analysis: Residualized Spatial Autocorrelation (RSA)**
**`5_GitHub_RSA_Mechanism.ipynb`**

This notebook implements the paper’s core **mechanism test**.

Main tasks:
- Aggregates outcomes to county and city levels.
- Residualizes observed clicks using behavioral model predictions.
- Tests for remaining spatial dependence using Moran’s I and Geary’s C.
- Compares results for **sparse vs. rich behavioral histories**.

**Interpretation:**  
If spatial autocorrelation disappears after residualization, geography does not provide independent information beyond behavior.

**Output:** Mechanism evidence supporting the interpretation of geographic effects.

---

### **6. Empirical Results & Policy Evaluation**
**`6_GitHub_Empirical_Results.ipynb`**

This notebook synthesizes all components into the paper’s main results.

Main tasks:
- Evaluates policy value using **Inverse Propensity Scoring (IPS)**.
- Compares information regimes across impression depth.
- Tests **complementarity vs. substitutability** between geographic and behavioral data.
- Produces the main tables and figures used in the paper.

**Output:** Final empirical results, figures, and tables.

---

## Conceptual Flow

The pipeline follows a clear identification logic:

> **Raw Data → Behavioral Learning → Logging Correction → Counterfactual Policies → Mechanism Tests → Economic Conclusions**

Each notebook depends only on outputs from earlier stages, ensuring modularity and interpretability.

---

## Requirements

- Python 3.9+
- pandas, numpy, scipy
- scikit-learn
- PyTorch (for LSTM)
- xgboost
- geopandas, libpysal, esda (for spatial analysis)
- matplotlib, seaborn

---

## Reproducibility Notes

- Notebooks are intended to be run **in numerical order (1 → 6)**.
- Random seeds are fixed where applicable.
- All policy evaluation uses **out-of-sample predictions** and **logged propensities**.
- Cluster-robust inference is performed at the user level.

---

## Research Contribution

This codebase operationalizes a central insight of the paper:

> **Geographic information is valuable when behavioral histories are sparse, but becomes largely redundant once rich behavioral data are available.**

The repository provides a transparent, end-to-end implementation of this argument, combining modern machine learning with causal policy evaluation and spatial econometrics.

---

## Citation

If you use this code or build on it, please cite the associated paper (details to be added upon publication).

---

For questions or clarifications, feel free to open an issue or contact the author.
