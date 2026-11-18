# S&P 500 1-Day Horizon Return & Price Prediction

This repository contains the code, analysis, and predictive modeling workflow to forecast the daily returns and price direction of the S&P 500 index (`^GSPC`) using various Machine Learning algorithms.

The project explores the entire quantitative development lifecycle: data acquisition, time-series specific feature engineering, chronological hyperparameter tuning, and rigorous statistical evaluation using the Diebold-Mariano significance test alongside realistic walk-forward backtesting with transaction costs.

## 📌 Project Overview

*   **Target Variable:** Daily return of the S&P 500 index on the next trading day ($t+1$).
*   **Data Source:** Yahoo Finance (`^GSPC`) from January 2021 to December 2025.
*   **Validation Methodology:**
    *   Chronological split with no shuffling (74% Train, 26% Test).
    *   **Walk-Forward Validation** with periodic model refitting every 21 trading days (monthly).
*   **Key Evaluation Metrics:** MAE & RMSE on returns, Directional Accuracy, MAE converted to index price points, Diebold-Mariano test, and portfolio simulation (backtesting).

## 🛠️ Feature Engineering

To prevent lookahead bias, all features are calculated strictly using historical data available up to day $t$:
1.  **Return Lags:** Lag 0 (today's return), Lag 1, Lag 2, and Lag 4.
2.  **High-Low Range:** The high-to-low range normalized by the closing price.
3.  **Open-Close Change:** Percentage change between the open and close price.
4.  **Moving Average Ratios:** Ratio of closing price to its 5-day and 20-day simple moving averages.
5.  **Volatility:** Rolling standard deviation of returns over a 5-day and 20-day window.
6.  **Volume Change:** Daily percentage change in trading volume.

## 🤖 Models Evaluated

*   **Baseline (Historical Mean):** A Dummy Regressor predicting the mean return of the training set.
*   **Linear Regression:** Standard Ordinary Least Squares (OLS) regression.
*   **Ridge Regression:** Linear regression with L2 regularization (tuned via `GridSearchCV` + `TimeSeriesSplit`).
*   **Decision Tree:** A non-linear regressor tuned for optimal depth and leaf size.
*   **Random Forest:** A bagging ensemble consisting of 200 decision trees.
*   **Gradient Boosting:** An additive boosting ensemble (achieved the best static price MAE during testing).

## 📊 Main Results & Key Findings

### 1. Static Model Performance (No Refit)
| Model | MAE return | RMSE return | $R^2$ | Directional Accuracy | MAE Price (Points) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Baseline (Train Mean)** | 0.00689 | 0.01102 | -0.00046 | **57.32%** | **41.17** |
| **Gradient Boosting** | 0.00692 | 0.01094 | 0.01355 | 55.45% | 41.38 |
| *Persistence (Tomorrow = Today)* | 0.00694 | 0.01104 | -0.00402 | - | 41.46 |
| **Ridge** | 0.00694 | 0.01098 | 0.00643 | 52.34% | 41.47 |
| **Random Forest** | 0.00697 | 0.01107 | -0.00991 | 52.34% | 41.68 |
| **Linear Regression** | 0.00698 | 0.01089 | **0.02339** | 52.65% | 41.80 |

### 2. Walk-Forward Validation Results (Refit Every 21 Days)
Dynamic validation via rolling refits yields highly realistic performance estimates:
*   **Decision Tree** emerged as the top-performing model in terms of trading strategy returns.
*   **Diebold-Mariano Test:** Statistical tests confirm that none of the machine learning models significantly outperformed the historical mean baseline at a 95% confidence level ($p	ext{-value} > 0.05$). This strongly supports the Efficient Market Hypothesis (EMH).

### 3. Backtest Simulation (Including 0.05% Transaction Cost)
Using a basic trading strategy (if predicted return > 0, *Buy/Hold*; otherwise, *Go Cash/Sell*), the cumulative growth of $1 over the testing period is:

*   **Decision Tree:** **$1.297** (19 trades)
*   **Buy & Hold (S&P 500 Index):** **$1.227**
*   **Ridge:** $1.184 (80 trades)
*   **Random Forest:** $1.173 (49 trades)
*   **Gradient Boosting:** $1.125 (33 trades)
*   **Linear Regression:** $1.081 (108 trades)

## 🚀 Getting Started

1.  Clone this repository:
    ```bash
    git clone https://github.com/username/sp500-prediction.git
    cd sp500-prediction
    ```
2.  Install the required dependencies:
    ```bash
    pip install numpy pandas matplotlib yfinance scikit-learn scipy
    ```
3.  Open the Jupyter notebook or run the scripts to reproduce the data processing, model training, and backtesting visualizations.

## 📝 License
This project is distributed under the MIT License. It is intended strictly for educational and research purposes. None of the contents should be construed as professional financial or investment advice.
```
