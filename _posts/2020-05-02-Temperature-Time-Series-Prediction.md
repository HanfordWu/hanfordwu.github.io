### Seasonal ARIMA(p,d,q)(P,D,Q,S):
1. Non seasonal ARIMA:
We can split the model into three parts: AR(p), I(d), MA(q):
- AR(p): This is auto-regressive model, current data is multiple linear regression of last p data.
p represent the number of history data we are going to use to predict current data. There is also a
random white noise counted. [reference](https://en.wikipedia.org/wiki/Autoregressive_model)
Eg. the temperature of next hour has a high relationship with its last p hours, we are using AR(p) to forecast next hour's temperature.
$$\hat{y}_{t} = \mu + \theta_{1}Y_{t-1} + ... + \theta_{p}Y_{t-p} + \varepsilon$$ 
is the white noise term.

- I(d) is the differential part. It is used when data is not stable. The model of ARMA(auto regressive moving average) require data to be stable. That's why before we use whatever ARMA or ARIMA, we have to check if the data is stable, find the order of difference: d. When we are checking stability of data, we do 1 or 2 or 3, etc. difference until the p-value is small enough, then we can know parameter d.
- MA(q) stands for move average model part, q represents the number of lagged forecast **error**. $$\hat{y}_{t} = \mu - \Theta_{1}\varepsilon_{t-1} + ... + \Theta_{q}\varepsilon_{t-q}$$

2. Seasonal ARIMA:
The concept of parameter P, D, Q are similar to non-seasonal ARIMA, but season is considering a period. All parameter is considering an interval of one period.  

The model:
```python
mod = sm.tsa.statespace.SARIMAX(df.temp, trend='n', order=(1,2,1), seasonal_order=(1,1,1,24))
```
`order` is ARIMA(p,d,q), `seasonal_order` is SARIMA(P,D,Q,S), S is the number of lag.
Before we use SARIMA, we need to decide p,d,q,P,D,Q,S.
Steps:
1. Check if data is stable. Dickey-Fuller test, tool:
`dftest = adfuller(timeseries, autolag='AIC')`.
2. If not stable, try test 1-order differential(This means d = 1):
```python
df['log_first_difference'] = df.riders_log - df.riders_log.shift(1)
# Note that 2-order differential is not shift(2)
```
Then do Dickey-Fuller test on difference to check if it is stable. 
3. If still not stable, add 1-order seasonal differential(D=1). Check Dickey-Fuller's p-value. Do difference until the data is stable.
4. Check "autocorrelation" and Partial autocorrelation to find p and q:
```python
fig = plt.figure(figsize=(12,8))
ax1 = fig.add_subplot(211)
fig = sm.graphics.tsa.plot_acf(df_Month.temp, lags=24, ax=ax1) #从13开始是因为做季节性差分时window是12
ax2 = fig.add_subplot(212)
fig = sm.graphics.tsa.plot_pacf(df_Month.temp, lags=80, ax=ax2)
```
The rule of adding SAR or SMA is: If the autocorrelation at the seasonal period is positive, consider adding an SAR term to the model. If the autocorrelation at the seasonal period is negative, consider adding an SMA term to the model. Try to avoid mixing SAR and SMA terms in the same model, and avoid using more than one of either kind.
Usually SAR(1) or SMA(1) is sufficient, that means when we predict one data, we only consider the value and the error of last period at this point.