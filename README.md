# Pair Trading Using ESABO Co-Movement Analysis

**MSc Thesis Implementation** | *University of Birmingham*
**Author:** Chinmay Kalvade

## 📖 Project Overview

This repository contains the code and research for the MSc thesis **"Pair Trading Using ESABO Co-Movement Analysis"**. 

The project adapts a novel algorithm from computational biology **ESABO (Entropy Shifts of Abundance Vectors under Boolean Operations)** to financial markets. Originally designed to detect microbial interactions, this method is modified here to identify non-linear co-movement patterns in S&P 500 stocks. Base paper: Claussen JC, Skiecevičienė J, Wang J, Rausch P, Karlsen TH, et al. (2017) Boolean analysis reveals systematic interactions among low-abundance species in the human gut microbiome. PLOS Computational Biology 13(6): e1005361. https://doi.org/10.1371/journal.pcbi.1005361

Unlike traditional cointegration methods (Engle-Granger, Johansen) that rely on linear equilibrium, ESABO uses **information theory (Shannon Entropy)** and **Boolean logic** to detect synergistic behavior in binarized price series.

## 📊 Key Performance Highlights

The research evaluates the strategy in two phases: a raw signal benchmark against industry standards, and an optimized trading engine application.

| Strategy Configuration | Sharpe Ratio | Annualized Volatility | Notes |
| :--- | :--- | :--- | :--- |
| **ESABO + Hurst Filter (Optimized)** | **1.90** | ~10% | *Best performing config (Fig 8.1 in Thesis)* |
| **Dynamic Beta Strategy** | **1.16** | - | *Adaptive hedge ratio approach* |
| **Engle-Granger (Benchmark)** | 1.11 | 19.08% | *High return, high volatility* |
| **ESABO (Raw Signal Benchmark)** | 0.75 | **11.84%** | *Lowest volatility profile* |
| **Johansen (Benchmark)** | 0.76 | 25.39% | *High volatility* |

## 📂 Repository Structure

* **`ESABO_Benchmark final.ipynb`**
    * *Thesis Chapter 7*: The core comparative study.
    * Implements the full ESABO pipeline (Binarization → Entropy → Null Model).
    * Runs side-by-side backtests against Engle-Granger and Johansen cointegration tests on S&P 500 data (2019–2025).
    
* **`Top ESABO trade strategies.ipynb`**
    * *Thesis Chapter 8*: Strategy optimization engine.
    * Contains the **Unified Backtesting Framework**.
    * Implements advanced execution logic: `VolSwitch`, `DynamicBeta`, `RegimeSwitching`, and `TrendAmplification`.

* **`Hurst exponent experiment.ipynb`**
    * *Thesis Chapter 2 & 5*: Signal validation.
    * Isolates the Hurst Exponent ($H$) to filter for mean-reverting character ($H < 0.5$). This was the key factor in boosting the Sharpe ratio from 0.75 to 1.90.

* **`correlation analysis.ipynb`**
    * *Thesis Chapter 4*: Data exploration.
    * Features LSTM + MLP neural networks for price prediction and feature importance analysis on 138+ fundamental factors.

## ⚙️ Methodology: The ESABO Pipeline

This project moves beyond standard correlation by implementing a 5-step signal generation process:

1.  **Robust Binarization**:
    * Converts continuous returns into binary states (0/1).
    * Uses **Robust Z-Score** (based on Median & MAD) rather than Mean/StdDev to handle financial "fat tails."
    * *Formula*: $z_t = (r_t - \text{median}) / \text{MAD}$
2.  **Boolean Co-occurrence**:
    * Applies Boolean `AND` operations to pair vectors to isolate synergistic movement.
3.  **Entropy Calculation**:
    * Computes the Shannon Entropy of the co-occurrence vector: $H = -\sum p_k \log p_k$.
4.  **Null Model Generation**:
    * Bootstraps (shuffles) the data 1,000 times to create a random distribution.
5.  **ESABO Score**:
    * Calculates the Z-score of the *observed* entropy against the *null* distribution. A high score indicates statistically significant co-movement.

## 🧠 Quant Terminology & HFT Concepts

### 1. Statistical Arbitrage (StatArb)
* **Cointegration**: Full implementation of Engle-Granger and Johansen tests for stationarity ($I(0)$ spreads).
* **Mean Reversion**: Utilization of **Ornstein-Uhlenbeck** assumptions and **Hurst Exponents** to quantify the speed of reversion.

### 2. Robust Statistics & Signal Processing
* **Non-Parametric Analysis**: ESABO does not assume Gaussian distributions, making it robust to market anomalies.
* **Median Absolute Deviation (MAD)**: Used for robust scaling to prevent outliers (e.g., earnings shocks) from skewing signal generation.
* **Information Theory**: Application of Shannon Entropy to financial time-series.

### 3. Execution & Risk Management
* **Walk-Forward Optimization**: Dynamic rolling windows (Training vs. Trading periods) to eliminate **Look-Ahead Bias**.
* **Volatility Targeting**: The `VolSwitch` strategy dynamically scales position sizing based on realized volatility regimes (Heteroskedasticity adjustment).
* **Vectorization**: Heavy use of `numpy` and `pandas` vectorization for efficient backtesting over 125,000+ potential pairs.

## 🛠️ Libraries Installation

```bash
pip install yfinance pandas numpy statsmodels joblib matplotlib seaborn tensorflow scikit-learn
