# Sugar Price Forecasting in Pakistan

A time-series forecasting project that analyzes weekly sugar prices in Pakistan and compares multiple forecasting approaches, including ARIMA, Auto ARIMA, Holt-Winters Exponential Smoothing, and Random Forest regression.

> **Note:** The original project report uses the phrase *sugarcane prices* in its title, but the dataset and analysis focus on **retail sugar prices in PKR per kilogram**.

## Project Overview

Sugar is a major agricultural and consumer commodity in Pakistan. Changes in its price affect households, farmers, manufacturers, retailers, and government policy.

This project uses historical weekly sugar-price data to:

- examine long-term and seasonal price behavior;
- identify volatility and structural changes over time;
- test whether the series is stationary;
- build and evaluate multiple forecasting models;
- determine which model produces the most accurate one-step-ahead predictions.

The analysis covers weekly observations from **3 January 2013 to 13 March 2025**.

## Key Objectives

1. Clean and prepare historical sugar-price data for analysis.
2. Compare price behavior across major cities in Pakistan.
3. Analyze national price trends, yearly variation, and seasonality.
4. Test the national price series for stationarity.
5. Build forecasting models using:
   - ARIMA
   - Auto ARIMA
   - Holt-Winters Exponential Smoothing
   - Random Forest Regression
6. Compare model performance using:
   - Mean Absolute Error (MAE)
   - Mean Squared Error (MSE)
   - Root Mean Squared Error (RMSE)
   - Mean Absolute Percentage Error (MAPE)
7. identify the most suitable model for short-term sugar-price forecasting.

## Dataset

The dataset was obtained from the **Karandaaz Data Portal** from the dataset titled **Sugar Price**.

### Coverage

- **Frequency:** Weekly
- **Period:** January 2013 to March 2025
- **Rows after cleaning:** 574
- **Geographic fields:** 18 Pakistani cities
- **National field:** `Pakistan`
- **Unit:** PKR per kilogram

### City Columns

The dataset contains prices for:

- Islamabad
- Rawalpindi
- Gujranwala
- Sialkot
- Lahore
- Faisalabad
- Sargodha
- Multan
- Bahawalpur
- Karachi
- Hyderabad
- Sukkur
- Larkana
- Peshawar
- Bannu
- Quetta
- Khuzdar
- Pakistan national average

### Expected Input File

Place the source CSV in the project directory using the filename:

```text
Sugar Price.csv
```

The first row of the original file contains unit labels such as `PKR`, so it is removed during preprocessing.

## Tech Stack

- Python
- Jupyter Notebook or Google Colab
- pandas
- NumPy
- Matplotlib
- Seaborn
- statsmodels
- pmdarima
- scikit-learn

## Forecasting Methods

### 1. ARIMA

A manually specified `ARIMA(1, 1, 1)` model was tested after applying first-order differencing.

The initial model did not forecast the series accurately and produced residual issues, including:

- remaining autocorrelation;
- heteroskedasticity;
- non-normal residuals;
- poor adaptation to changing price levels.

Because of these limitations, Auto ARIMA was used as an improved alternative.

### 2. Auto ARIMA

Auto ARIMA automatically searches for an appropriate ARIMA configuration for each rolling training window.

The model was evaluated using a sliding-window, one-step-ahead forecasting process.

### 3. Holt-Winters Exponential Smoothing

A multiplicative Holt-Winters model was used to capture:

- trend;
- seasonality;
- changes in seasonal magnitude as the price level increased.

### 4. Random Forest Regression

Random Forest was applied as a supervised machine-learning forecasting model using lagged price features.

Two window configurations were tested:

- a window containing 70% of the historical observations;
- a fixed rolling window of 100 observations.

The 100-observation window performed substantially better.

## Analysis Workflow

```text
Raw weekly price data
        |
        v
Data cleaning and type conversion
        |
        v
Exploratory data analysis
        |
        v
Trend, volatility, and seasonality analysis
        |
        v
ADF stationarity test
        |
        v
First-order differencing
        |
        v
ACF and PACF analysis
        |
        v
Model training with rolling windows
        |
        v
One-step-ahead predictions
        |
        v
Model evaluation and comparison
```

## How to Run the Project

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

Activate it on Windows:

```bash
.venv\Scripts\activate
```

Activate it on macOS or Linux:

```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install numpy pandas matplotlib seaborn statsmodels pmdarima scikit-learn jupyter
```

Optionally, save the dependencies:

```bash
pip freeze > requirements.txt
```

### 4. Add the Dataset

Place the dataset in the repository root:

```text
Sugar Price.csv
```

The notebook expects the file to be available using:

```python
pd.read_csv("Sugar Price.csv")
```

### 5. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open the project notebook and run the cells from top to bottom.

### 6. Run in Google Colab

You may also upload the notebook and dataset to Google Colab.

If the files are stored in Google Drive, mount the drive and update the working directory before loading the CSV.

## Data Preparation

The preprocessing stage performs the following steps:

1. Loads the CSV file.
2. Removes the first row containing unit labels.
3. Converts `Period End` to a datetime field.
4. Converts all price columns to numeric values.
5. Checks for missing values.
6. Checks for duplicate records.
7. Creates additional date fields:
   - `Year`
   - `Month`
   - `Month Name`
8. Sorts the observations chronologically before model training.

Example:

```python
import pandas as pd

df = pd.read_csv("Sugar Price.csv")
df = df.iloc[1:].copy()

df["Period End"] = pd.to_datetime(
    df["Period End"],
    format="%d-%b-%Y"
)

for column in df.columns[1:]:
    df[column] = pd.to_numeric(df[column], errors="coerce")

df = df.sort_values("Period End").reset_index(drop=True)
```

## Stationarity Testing

The Augmented Dickey-Fuller test was applied to the national `Pakistan` price series.

### Original Series

- ADF statistic: approximately `-2.04`
- p-value: approximately `0.268`

Because the p-value was greater than `0.05`, the original series was treated as non-stationary.

### First-Order Differenced Series

- ADF statistic: approximately `-9.62`
- p-value: approximately `1.70 × 10^-16`

The differenced series was therefore considered stationary.

## Model Evaluation

The models were assessed using four error metrics.

| Model | MAE | MSE | RMSE | MAPE |
|---|---:|---:|---:|---:|
| Holt-Winters | 2.0779 | 13.1058 | 3.6202 | 1.5911% |
| Auto ARIMA | 1.9827 | 12.7064 | 3.5646 | 1.5060% |
| Random Forest — 70% window | 8.8257 | 162.5990 | 12.7514 | 7.0306% |
| Random Forest — 100-point window | **1.8427** | **9.6057** | **3.0993** | 1.8642% |

## Final Results

### Best Overall Model

The **Random Forest model using a 100-observation rolling window** produced the best overall performance.

It achieved the lowest:

- MAE: `1.84`
- MSE: `9.61`
- RMSE: `3.10`

Its predictions followed actual price movements closely and captured nonlinear changes, peaks, troughs, and rapid trend shifts more effectively than the traditional time-series models.

Auto ARIMA produced the lowest reported MAPE at approximately `1.51%`, while the 100-point Random Forest model performed best across the main absolute and squared-error measures.

### Model Interpretation

- **Random Forest, 100-point window:** Best at adapting to nonlinear and short-term price behavior.
- **Auto ARIMA:** Strong and interpretable statistical baseline with good average percentage accuracy.
- **Holt-Winters:** Effective for trend and seasonality but tends to smooth sudden price shocks.
- **Random Forest, 70% window:** Performed poorly, showing that model quality depends heavily on rolling-window design.

## Key Insights

### Long-Term Price Trend

Sugar prices increased considerably over the study period.

The national mean price increased from approximately:

- PKR `53.96` per kg in 2013
- to PKR `142.11` in 2024
- and PKR `153.25` in the available 2025 observations

The strongest rise occurred after 2020.

### Price Volatility

- Prices were relatively stable from 2013 to 2018.
- Volatility increased from 2019 onward.
- 2023 showed the greatest yearly variation, with a standard deviation of approximately `23.39`.
- Prices remained high in 2024, although variation was lower than in 2023.

### Geographic Differences

Across the complete period:

- Islamabad had the highest average city price at approximately PKR `87.98` per kg.
- Sukkur had the lowest average city price at approximately PKR `80.34` per kg.
- Quetta recorded some of the highest maximum prices.

The differences may reflect local supply, transport costs, market demand, and distance from production areas. These explanations should be treated as possible interpretations rather than causal conclusions.

### Seasonality

Seasonal decomposition indicated a recurring yearly pattern in weekly national sugar prices.

However, the growing price level and changing volatility mean that seasonality alone is not sufficient to explain the series.

## Practical Applications

The forecasting workflow may support:

- agricultural production planning;
- government price monitoring;
- inventory and procurement decisions;
- consumer-protection planning;
- early warning systems for price surges;
- supply-chain and import-policy decisions.

## Limitations

- The analysis uses only historical sugar prices and does not include external variables such as inflation, exchange rates, fuel prices, production volume, imports, exports, weather, or government policy.
- One-step-ahead rolling evaluation does not guarantee equal performance for long-horizon forecasts.
- The dataset contains weekly observations but some seasonal model configurations use a period of 12, while weekly annual seasonality would generally correspond to approximately 52 observations. This should be reviewed in future versions.
- Some explanations of price changes are observational and should not be interpreted as proven causal relationships.
- Random Forest forecasts depend strongly on feature construction and rolling-window size.
- The 2025 yearly statistics are based only on observations available through 13 March 2025.

## Future Improvements

- Add macroeconomic and agricultural predictors.
- Compare weekly seasonal periods consistently across models.
- Add SARIMA, XGBoost, LightGBM, Prophet, and LSTM benchmarks.
- Use time-series cross-validation instead of a single rolling configuration.
- Tune Random Forest hyperparameters systematically.
- Generate prediction intervals.
- Package the workflow into reusable Python scripts.
- Build an interactive dashboard for forecast monitoring.
- Automate updates when new weekly data becomes available.

## Suggested Repository Structure

```text
.
├── README.md
├── Sugar Price.csv
├── notebooks/
│   └── sugar_price_forecasting.ipynb
├── reports/
│   └── Prediction of Sugarcane Prices in Pakistan using Time Series Methods.pdf
├── images/
│   ├── overall_price_trend.png
│   ├── seasonal_decomposition.png
│   ├── model_comparison.png
│   └── actual_vs_predicted.png
├── requirements.txt
└── .gitignore
```

## Reproducibility Notes

To obtain results consistent with the report:

- sort the series in ascending date order;
- use the `Pakistan` column as the forecasting target;
- use one-step-ahead rolling forecasts;
- update each rolling window with the newly observed actual value;
- set `random_state=42` for Random Forest;
- use a fixed 100-observation window for the strongest Random Forest result.

## Acknowledgements

The historical sugar-price dataset was sourced from the Karandaaz Data Portal.

## License

Add the license that best matches your intended use. The MIT License is commonly used for public portfolio projects.

---

This repository is intended as a data-science portfolio project demonstrating time-series analysis, forecasting, model evaluation, and applied interpretation using historical commodity-price data from Pakistan.
