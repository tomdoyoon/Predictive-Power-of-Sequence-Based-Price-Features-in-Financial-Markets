# Predictive-Power-of-Sequence-Based-Price-Features-in-Financial-Markets

# Abstract
Financial markets are complex and dynamic systems, with asset prices influenced by a multitude of factors, including economic indicators, geopolitical events, and market sentiment. Despite these challenges, researchers and practitioners continue to seek innovative methods for predicting price movements and optimizing investment strategies. This study focuses on the predictive power of endogenous price sequences, examining the extent to which historical price data can be leveraged to forecast next-day price classifications across various financial markets. 

## Data Source

The daily data for 17 ETFs is obtained from Alpha Vantage, a free API provider for historical and real-time financial data.
- SPY: SPDR S&P 500 ETF Trust
- IJH: iShares Core S&P Mid-Cap ETF
- IWM: iShares Russell 2000 ETF
- QQQ: Invesco QQQ ETF
- DIA: SPDR Dow Jones Industrial Average ETF Trust
- EWJ: iShares MSCI Japan ETF
- EWY: iShares MSCI South Korea ETF
- EWC: iShares MSCI Canada ETF
- EWU: iShares MSCI United Kingdom ETF
- EWA: iShares MSCI Australia ETF
- EWG: iShares MSCI Germany ETF
- EWZ: iShares MSCI Brazil ETF
- GLD: SPDR Gold Shares ETF
- SLV: iShares Silver Trust ETF
- DBE: Invesco DB Energy Fund
- DBA: Invesco DB Agriculture Fund
- DBB: Invesco DB Base Metals Fund

The data is pulled using the Alpha Vantage API, with a free API key obtained from https://www.alphavantage.co/support/#api-key.
The API documentation for Alpha Vantage can be found at nhttps://www.alphavantage.co/documentation/.

## Data Preprocessing

The raw data obtained from Alpha Vantage contains post-split prices.
To normalize the prices, a custom split adjustment is applied to transform the post-split prices back to their pre-split equivalents.
This adjustment involves dividing the post-split price by the split ratio (e.g., 2 for a 2:1 split) to obtain the pre-split price.
Additional Data Sources

Split and dividend information for the ETFs is obtained from Seeking Alpha.
The data is last updated on 3/29/2025.
The sources for this data can be found in the "etf_corp_actions.xlsx" spreadsheet, with links to:
https://seekingalpha.com/symbol/VOO/splits
https://seekingalpha.com/symbol/VTWO/dividends/history

Note that dividend information is not used in the analysis.

# Conclusion

This study compared the performance of XGBoost and LSTM models on six different datasets, including five asset classes (US Equity, International Equity, Precious Metals, Commodities) and the S&P 500 index (SPY). The results showed that the XGBoost model performed similarly across all datasets, with test accuracy ranging from 0.285 to 0.363, log loss between 1.848 and 1.922, and ROC-AUC scores between 0.573 and 0.627. The LSTM model, trained on the SPY dataset, achieved an accuracy of 0.328, log loss of 1.805, and ROC-AUC of 0.55. Since the LSTM model trained on the SPY dataset did not outperform the XGBoost model, the other datasets were not used to train an LSTM model, as it was deemed unlikely to provide significant performance gains. 

The accuracy range of around 30% across all models suggests that endogenous price features have a weak ability to predict the following day's price classification. However, this level of accuracy may still be sufficient for a portfolio manager or trader to achieve profitable trading outcomes, depending on their specific setup and risk-reward preferences. This study demonstrates that it is feasible to somewhat predict next-day price classification using price features alone, allowing market participants to structure trading strategies around these features, especially when combined with proper risk management and reward steps.

# Table of Contents

Please refer to the following table of contents to navigate your exploration:

Data Preparation: Reproducible steps for processing raw data.

XGBoost: Application of an XGBoost model to predict price classification (see below).

LSTM: Application of a LSTM model to predict price classification (see below).

# Feature Engineering

## Price Classification

The price classification logic assigns a class label to each data point based on the relationship between the current and previous day's prices. The classification logic is as follows:

The price movement is classified into eight categories:
- U_UP: The current day's high and low prices are both higher than the previous day's high and low prices, and the current close price is higher than the current open price.
- D_UP: The current day's high and low prices are both higher than the previous day's high and low prices, but the current close price is lower than or equal to the current open price.
- D_DOWN: The current day's high and low prices are both lower than the previous day's high and low prices, and the current close price is lower than the current open price.
- U_DOWN: The current day's high and low prices are both lower than the previous day's high and low prices, but the current close price is higher than or equal to the current open price.
- U_OUT: The current day's high price is higher than or equal to the previous day's high price, and the current day's low price is lower than or equal to the previous day's low price, and the current close price is higher than or equal to the current open price.
- D_OUT: The current day's high price is higher than or equal to the previous day's high price, and the current day's low price is lower than or equal to the previous day's low price, and the current close price is lower than the current open price.
- U_IN: The current day's high price is lower than or equal to the previous day's high price, and the current day's low price is higher than or equal to the previous day's low price, and the current close price is higher than or equal to the current open price.
- D_IN: The current day's high price is lower than or equal to the previous day's high price, and the current day's low price is higher than or equal to the previous day's low price, but the current close price is lower than the current open price.

## Lag Features
The feature engineering process involves creating new features from the existing data to enhance the predictive power of the model. 

The following features are engineered over various time horizons, including 5, 10, 20, 40, 60, and 120 days:
- Rate of Change (ROC): Calculated by dividing the current open, high, low, and close prices by their respective historical values.
- Percentage to N-day Max/Min: Calculated by dividing the current open, high, low, and close prices by the maximum and minimum values of their respective prices over the specified time horizons.
- Coefficient of Variation (CV): Calculated by dividing the standard deviation of the closing prices by their mean, providing a measure of volatility relative to the average price movement.
