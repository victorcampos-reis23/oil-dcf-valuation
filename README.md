# Oil Asset Valuation — DCF + LSTM Price Forecasting

> **Quantitative valuation of an oil-producing asset combining a Long Short-Term Memory (LSTM) neural network for Brent Crude price forecasting with a Discounted Cash Flow (DCF) model. Produces P10 / P50 / P90 fair-value estimates under bull, base, and bear scenarios.**

---

## Motivation

Valuing oil assets is inherently forward-looking: the fair value depends critically on where oil prices will be over the next decade. Classical DCF models typically rely on analyst consensus or flat-price assumptions. This project replaces that static input with a data-driven LSTM forecast, making the valuation dynamic and scenario-aware.

---

## Methodology

```
Historical Brent Prices (yfinance)
        │
        ▼
Feature Engineering
(MA-20, MA-60, Volatility, Returns, RSI)
        │
        ▼
LSTM Neural Network
(128 → 64 units, Dropout, Huber Loss)
        │
        ▼
10-Year Price Forecast
        │
        ├── × 1.15  →  P90 (Bull)
        ├── × 1.00  →  P50 (Base)
        └── × 0.85  →  P10 (Bear)
                │
                ▼
        DCF Model
  (Revenue → EBIT → NOPAT → FCF → NPV + Terminal Value)
```

### LSTM Architecture

| Layer | Units | Notes |
|-------|-------|-------|
| LSTM  | 128   | return_sequences=True |
| Dropout | 20% | regularisation |
| LSTM  | 64    | return_sequences=False |
| Dropout | 20% | regularisation |
| Dense | 32    | ReLU activation |
| Dense | 1     | Price output |

- **Loss function**: Huber (robust to outliers in commodity prices)
- **Optimiser**: Adam
- **Look-back window**: 60 trading days
- **Features**: Close price, MA-20, MA-60, 20-day volatility, 1-day return, 5-day return, RSI-14

### DCF Assumptions

| Parameter | Value |
|-----------|-------|
| Production | 5,000 BOPD |
| OPEX | $20 / bbl |
| Royalty rate | 15% |
| Corporate tax | 25% |
| Sustaining CAPEX | $30 MM / year |
| WACC | 12% |
| Terminal growth | 2% |
| Projection horizon | 10 years |

---

## Results

The model outputs four charts (saved to `outputs/`):

| File | Description |
|------|-------------|
| `01_historical_price.png` | Brent Crude historical prices |
| `02_lstm_loss.png` | Training vs validation Huber loss |
| `03_lstm_vs_actual.png` | Out-of-sample prediction with MAPE |
| `04_dcf_scenarios.png` | NPV bar chart + annual price paths |

A `valuation_summary.csv` is also exported with NPV and year-1 price per scenario.

---

## How to Run

```bash
# 1. Clone the repo
git clone https://github.com/victorcampos-reis23/oil-dcf-valuation.git
cd oil-dcf-valuation

# 2. Install dependencies
pip install yfinance numpy pandas scikit-learn tensorflow matplotlib seaborn

# 3. Run the pipeline
python valuation.py
```

Expected runtime: ~3–5 minutes on CPU (LSTM training).

---

## Project Structure

```
oil-dcf-valuation/
├── valuation.py          # Main pipeline (data → LSTM → DCF → plots)
├── outputs/              # Auto-generated charts and CSV
│   ├── 01_historical_price.png
│   ├── 02_lstm_loss.png
│   ├── 03_lstm_vs_actual.png
│   ├── 04_dcf_scenarios.png
│   └── valuation_summary.csv
├── notebooks/            # Exploratory Jupyter notebooks (optional)
└── README.md
```

---

## Skills Demonstrated

- **Machine Learning**: LSTM architecture, sequence modelling, dropout regularisation, early stopping
- **Financial Modelling**: DCF, WACC, terminal value (Gordon Growth), scenario analysis
- **Time Series**: Feature engineering, rolling statistics, RSI, multi-step forecasting
- **Data Engineering**: Automated data collection (yfinance), MinMaxScaler pipeline, iterative forecasting loop
- **Visualisation**: Matplotlib / Seaborn publication-quality charts

---

## Author

**Victor Hugo Campos Reis Alves**
M.Sc. in Economics — UFPE/PIMES (2025)
Specialisation: Macroeconomics, Quantitative Methods, Commodity Markets

[LinkedIn](https://linkedin.com/in/victor-hugo-campos) · [GitHub](https://github.com/victorcampos-reis23)
