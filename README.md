# The-Privacy-Utility-Trade-Off-of-Location-Tracking-in-Ad-Personalization
This research studies whether geographical and behavioral data function as complements or substitutes in achieving business objectives, emphasizing the value–privacy trade-off. Geography adds value when behavioral histories are sparse, but becomes a weak substitute as histories grow rich, offering limited incremental gains relative to privacy risk.

## Data, Models, and Policy Evaluation Pipeline

This repository contains the **complete empirical pipeline** for the research paper studying the **value of geographical information relative to behavioral data in ad personalization**, and how this relationship evolves as users accumulate interaction histories. The project integrates **machine learning**, **causal inference**, and **spatial econometrics** to provide a transparent, reproducible analysis of when additional data in personalization systems is economically valuable and when it is redundant.

The guiding question of the paper is:

> *When does location data provide incremental value in ad targeting, and when is its role subsumed by behavioral history?*

---

### High-Level Pipeline

The analysis proceeds in six consecutive stages, each implemented in a dedicated notebook. Together, these stages form an end-to-end workflow:

Raw Impression Logs

↓

Data Preparation & Feature Engineering

↓

Sequential Behavioral Modeling

↓

Propensity Score Estimation

↓

Counterfactual Prediction & Policy Construction

↓

Mechanism Analysis (Residual Spatial Autocorrelation)

↓

Empirical Results & Policy Evaluation

---

### Project Structure

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

This notebook constructs the core analytical dataset at the **impression level**.

It cleans raw ad impression logs, enforces consistent user-level ordering, and applies sample restrictions such as limiting analysis to the first 150 impressions per user. The notebook engineers covariates for four distinct information regimes:

- **Contextual**: time, device, app, and environment variables.
- **Geographical**: location identifiers and spatial attributes.
- **Behavioral**: user interaction histories and past engagement.
- **Geographical + Behavioral**: combined information set.

Categorical variables are encoded, numerical features are scaled, and aligned train/test splits are created and reused throughout the project. The purpose of this step is to ensure that all downstream comparisons differ **only** in their information sets, not in sample composition or preprocessing choices.

**Output:** Model-ready datasets used by all subsequent notebooks.

---

### **2. Sequential Behavioral Modeling**
**`2_GitHub_LSTM_Training.ipynb`**

This notebook learns **behavioral representations** from user interaction histories using a sequential model.

User impressions are organized into ordered sequences, and an **LSTM model** is trained to predict click probability based solely on past behavior. This approach captures learning dynamics, persistence, and heterogeneity that static models cannot represent. The notebook produces out-of-sample behavioral predictions that are later used both as inputs to targeting models and as controls in mechanism analysis. This step operationalizes the idea that behavioral data are inherently sequential and that their predictive power grows as histories accumulate.

Main tasks:
- Constructs ordered impression sequences at the user level.
- Trains an **LSTM** to predict click probability based solely on past behavior.
- Captures dynamic engagement patterns such as learning, persistence, and habit formation.
- Produces out-of-sample behavioral predictions.

**Output:** Behavioral click predictions used for counterfactual analysis and mechanism tests.

---

### **3. Propensity Score Estimation**
**`3_GitHub_Propensity_Score.ipynb`**

This notebook estimates the **logging (assignment) policy** of the ad platform.

It models the probability that each ad is shown in each impression, producing estimated propensity scores \( \hat{\pi}_D(a \mid i) \). These propensities define the support of the observed policy and form the basis for causal reweighting. By explicitly modeling the logging policy, this step corrects for non-random ad assignment and enables unbiased policy evaluation.

Main tasks:
- Estimates ad-level propensity scores \( \hat\pi_D(a \mid i) \).
- Defines the support of valid counterfactual evaluation.
- Provides reweighting terms required for inverse propensity scoring (IPS).

**Output:** Propensity scores merged into policy evaluation datasets.

---

### **4. Counterfactual Prediction & Policy Construction**
**`4_GitHub_Xgboost.ipynb`**

This notebook generates **counterfactual outcomes** under alternative information regimes.

Gradient-boosted tree models (XGBoost) are trained separately under each regime—contextual, geographical, behavioral, and geographical plus behavioral. For each impression, the models predict counterfactual click probabilities for all candidate ads. These predictions are then used to construct candidate targeting policies, providing the inputs needed for policy evaluation. This stage produces the full counterfactual prediction matrices used in subsequent analysis.

Main tasks:
- Trains XGBoost and LSTM models using:
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

Impressions are aggregated to county and city levels, and observed clicks are residualized using behavioral predictions. The notebook then tests whether spatial dependence persists after conditioning on behavior using Moran’s I and Geary’s C. The analysis is conducted separately for users with **sparse** and **rich** behavioral histories.

If spatial autocorrelation disappears after residualization, geographical variation primarily reflects unobserved preferences already captured by behavior. Persistent autocorrelation indicates independent geographic effects. This RSA framework provides a principled way to identify the channel through which geographical data creates value.

Main tasks:
- Aggregates outcomes to county and city levels.
- Residualizes observed clicks using behavioral model predictions.
- Tests for remaining spatial dependence using Moran’s I and Geary’s C.
- Compares results for **sparse vs. rich behavioral histories**.

**Output:** Mechanism evidence supporting the interpretation of geographic effects.

---

### **6. Empirical Results & Policy Evaluation**
**`6_GitHub_Empirical_Results.ipynb`**

This notebook synthesizes all prior components and produces the **main empirical results** of the paper.

Policy value is estimated using **Inverse Propensity Scoring (IPS)**, correcting for the logged assignment mechanism. The notebook evaluates targeting policies across information regimes, examines how value evolves with impression depth, and tests complementarity versus substitutability between geographical and behavioral data using difference-in-differences estimands. Inference is conducted with **cluster-robust standard errors** at the user level, and all main tables and figures in the paper are generated here.

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

- **Python**: 3.9 or later  
- **Core scientific stack**: `numpy`, `pandas`, `scipy`  
- **Machine learning**: `scikit-learn`, `xgboost`  
- **Deep learning**: `torch` (PyTorch; used for sequential LSTM behavioral modeling)  
- **Spatial econometrics**: `geopandas`, `libpysal`, `esda`  
- **Visualization**: `matplotlib`, `seaborn`  

All experiments were run in a Python 3.9 environment. GPU support (CUDA) is optional but recommended for LSTM training.
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
