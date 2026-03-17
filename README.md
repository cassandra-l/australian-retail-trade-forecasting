# Retail Sales Forecasting in Australia

## Project Overview

In this project, Australian retail turnover data from the Australian Bureau of Statistics (ABS) is analysed and a time series forecasting models ide developed to predict future sales.

Two widely used forecasting methods are explored and compared:
- Exponential Smoothing (ETS)
- ARIMA (AutoRegressive Integrated Moving Average)

The goal is to determine which model provides more reliable forecasts for retail turnover.

---

## How to Run
1. Ensure you have **R** and **RStudio** installed.
2. Install the required libraries: `fpp3`, `tidyverse`, and `fable`.
3. Clone this repository and run the analysis script to reproduce the results.

---

## Data Source

The dataset is obtained from the **Australian Bureau of Statistics (ABS)**:

Dataset: *Retail Trade, Australia (Catalogue 8501.0)*

---

## Data Preparation 

The raw ABS dataset contains multiple time series including total aggregates and discontinued series. Several preprocessing steps were applied to prepare the data for analysis:

- Filtering retail turnover data by state
- Removing total industry aggregates
- Separating state and industry information
- Removing discontinued series
- Converting the dataset into a time series format

To illustrate the forecasting process, one series was randomly sampled from the dataset, The randomly selected series happened to correspond to the retailing turnover in New South Wales, which is used throughout the analysis.

---

## Exploratory Data Analysis

The first step was to visualise the retail turnover time series.

<img width="515" height="344" alt="image" src="https://github.com/user-attachments/assets/56b7e1c9-011c-4809-8455-7da1d1b6ae7b" />

<img width="508" height="362" alt="image" src="https://github.com/user-attachments/assets/54e0a01d-b05c-4cbf-87b7-289cbdb1d762" />

The plot reveals several key characteristics:
- A strong upward trend in retail turnover over time
- Clear seasonal patterns
- A noticeable disruption around 2020 likely due to COVID-19
- Increasing variance over time

---

## Seasonality Analysis

Seasonal patterns were explored using seasonal and subseries plots.

<img width="468" height="355" alt="image" src="https://github.com/user-attachments/assets/e32b7083-de03-4639-9d1a-57d0c547dad5" />

<img width="497" height="353" alt="image" src="https://github.com/user-attachments/assets/648a233d-fa1b-4220-8f9c-0bb90809d027" />

Retail turnover tends to peak toward the end of each year, which likely reflects increased consumer spending during the holiday season.

---

## Data Transformation

Since the variance increases over time, a **Box-Cox transformation** was applied to stabilise the variance.

Stationarity tests indicated that the series required:
- One seasonal difference
- One regular difference
to achieve stationarity before fitting ARIMA models.

---

## Forecasting Models

Two forecasting approaches were evaluated.

### ETS Model

ETS models capture the structure of time series data using:
- Error
- Trend
- Seasonality

Before fitting ETS models, several observations from the time plot guided our choices:
- The series shows both trend and seasonality, so models without trend or seasonality were excluded.
- The trend does not flatten over time, so damped trend models were excluded.
- Multiplicative trends were avoided because they can produce unstable forecasts in this dataset.
- Models like ETS(A,N,M), ETS(A,A,M), and ETS(A,Ad,M) were also excluded due to potential numerical difficulties.

Applying these restrictions, the candidate ETS models were:
- ETS(A,A,A)
- ETS(M,A,A)
- ETS(M,A,M)

The best performing model based on AICc and RMSE was:
**ETS(M,A,M)**


---

### ARIMA Model

ARIMA models were considered to capture both trend and seasonal patterns in the time series. 
The model selection was guided by the ACF and PACF plots:

<img width="479" height="166" alt="image" src="https://github.com/user-attachments/assets/6d00e69e-0572-4c33-a627-d24832a464f0" />

<img width="233" height="165" alt="image" src="https://github.com/user-attachments/assets/7a6cb8a3-5229-45ea-81dc-a1d37eb7305f" />

<img width="225" height="164" alt="image" src="https://github.com/user-attachments/assets/e47d55b7-bc00-49b1-ad34-9ff57e373db3" />

- The PACF plot showed significant spikes at lag 12 and lag 24. Within the first seasonal lag (before lag 12), there were two strong spikes, suggesting an autoregressive order P = 2 (AR(2)).
- The ACF plot indicated seasonal correlations at lag 12 and 24, which suggested a seasonal moving average order of Q = 1 or 2.  
- The non-seasonal moving average order q was set to 1, based on the significant spike at lag 1.
- Since the series had already been differenced twice (seasonal and non-seasonal), the constant term c was set to 0 to avoid a long-term quadratic trend. This ensures that forecasts follow a straight-line trend, consistent with the observed data.

Based on these choices, the candidate ARIMA models were:
- ARIMA(2,1,1)(2,1,1)[12]  
- ARIMA(2,1,1)(2,1,2)[12]

The best performing model based on AICc and RMSE was:
**ARIMA(2,1,1)(2,1,1)[12]**

---

## Model Comparison

To determine the most reliable forecasting model, the performance of the **ETS(M,A,M)** and **ARIMA(2,1,1)(2,1,1)[12]** models was compared using Residual Diagnostics and Forecast Accuracy on a test set.

### Residual Diagnostics
The residuals of both models were checked to ensure they behaved like "white noise."

- **Ljung-Box Test:** For both models, the Ljung-Box test returned small p-values, indicating that some autocorrelation remains in the residuals.
- **Visual Inspection:** Despite the test results, both models successfully captured the primary trend and the strong seasonality of the retail data.

### Forecast Accuracy
The dataset was split into a training set and a test set (the last 2 years of data). 
Based on the error metrics, the **ETS(M,A,M)** model achieved the highest accuracy**.
This performance advantage is visually evident in the forecast plots. The ETS forecast (blue line) aligns more naturally with the historical data (black line).
<img width="532" height="375" alt="image" src="https://github.com/user-attachments/assets/6fa83025-adb5-4108-bf17-4ff87c4f71d1" />

---

## Final Forecast Results

The 2-year forecast for New South Wales retail turnover highlights key differences between the two approaches:

- **ETS Performance:** The ETS model provided a better fit to the original data and achieved higher predictive accuracy on the test set. It appears more robust at capturing the multiplicative nature of the seasonal peaks in this specific retail series.
- **ARIMA Performance:** While the ARIMA model effectively captured the trend and seasonality, its forecast was slightly less accurate compared to the ETS model for this specific series.

---

## Conclusion

For the analysis of Retailing turnover in New South Wales, the **ETS(M,A,M)** model is the preferred choice. It demonstrated superior predictive accuracy and a better ability to handle the increasing variance observed in the retail sector over time.

While both models are powerful tools for time series analysis, this project highlights that for retail data with strong, evolving seasonal patterns, Exponential Smoothing (ETS) can often outperform ARIMA.

---
