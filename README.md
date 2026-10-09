# Bitcoin Daily Price Forecasting: Regression Baselines and Stationarity Analysis

Exploratory analysis of daily BTC/USD closing prices with linear and polynomial regression on time, Augmented Dickey-Fuller stationarity tests, ACF/PACF plots and an attempted rolling ARIMA evaluation.

**Interactive demo:** [Run it in your browser](https://laiba-khawar-portfolio.vercel.app/work/btc-forecasting#demo)

The demo re-runs the forecasts with a chronological train/test split instead of the random split used in the notebook.

## Data

`BTC-Daily.csv`: 2,651 daily rows from 2014-11-28 to 2022-03-01, newest first. Columns: `unix`, `date`, `symbol` (BTC/USD), `open`, `high`, `low`, `close`, `Volume BTC`, `Volume USD`. The notebook finds no missing values.

## What the notebook does (`i2112697_A4.ipynb`)

1. **Exploration (cells 1 to 5):** summary statistics, missing-value check, min-max normalised close, date range and a plot of closing prices.
2. **Linear regression (cell 7):** feature = Unix timestamp, target = close, `train_test_split(test_size=0.2, random_state=42)`.
3. **Polynomial regression (cell 10):** same split, degree-2 polynomial features of the timestamp.
4. **Stationarity (cell 12):** ADF test on the close series and on its first difference, then ACF and PACF plots (30 lags) of the differenced series.
5. **Rolling-window evaluation (cell 15):** a 30-day window refitted for each one-step-ahead prediction with linear and degree-2 polynomial regression.
6. **Rolling ARIMA (cell 16):** ARIMA(5,1,0) fitted on each 30-day window.

## Results

| Model (random 80/20 split) | RMSE | MAE | MAPE | Source |
| --- | --- | --- | --- | --- |
| Linear regression on timestamp | 10160.62 | 7857.78 | 511.48% | cell 7 |
| Polynomial regression, degree 2 | 7444.31 | 5338.78 | 218.00% | cell 10 |

Augmented Dickey-Fuller tests (cell 12):

| Series | ADF statistic | p-value | Conclusion |
| --- | --- | --- | --- |
| Close price | -1.918 | 0.3236 | Cannot reject a unit root (non-stationary) |
| First difference of close | -8.529 | 1.05e-13 | Stationary |

The very large MAPE values come from predicting a price trend from the date alone: in the early years prices were in the hundreds of dollars, so absolute errors of several thousand dollars become errors of several hundred percent.

## Repository contents

- `i2112697_A4.ipynb`: the analysis notebook.
- `BTC-Daily.csv`: the dataset.
- `requirements.txt`: jupyter, matplotlib, numpy, pandas, scikit-learn, statsmodels.

## Running it

The notebook was last run with Python 3.10.

```bash
git clone https://github.com/LaibaKhawar/Time-Series-Analysis.git
cd Time-Series-Analysis
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook i2112697_A4.ipynb
```

The notebook reads `BTC-Daily.csv` from the working directory, so start Jupyter from the repository root.

## Known limitations

- **Random split leaks future data.** The regression results use `train_test_split` with shuffling, so the models are trained on days after the days they are tested on. The numbers are not forecasting accuracy.
- **Rolling-window printout shows only two windows.** Cell 15 stores metrics for every window, but its print loop zips the model names `['LR', 'Poly']` with the per-window list, so it prints the values of the first two windows only, not averages over all windows. The data is also still in newest-first order at that point, so each window is used to predict the day before it.
- **The ARIMA cell failed.** Every window in cell 16 raised `Singleton array ... cannot be considered a valid collection` (a scalar forecast was passed to scikit-learn's metric functions), and the cell was stopped with a KeyboardInterrupt. There are no ARIMA results.
- Only the date is used as a feature; open, high, low and volume are not used for forecasting.

## Author

[Laiba Khawar](https://github.com/LaibaKhawar) · [LinkedIn](https://www.linkedin.com/in/laiba-k-00b2b1249/)
