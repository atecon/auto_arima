# auto_arima

Returns best SARIMAX model according to either the AIC, the AICc, the BIC or the HQC information criteria. The function conducts a (brute force) search over possible model within the order constraints provided.

This is an alternative package to the existing gretl *armax* package written by Yi-Nung Yang which has some limitations, e.g. it does only work for ARMA type models.

The features of the *auto_arima* package are:
- Consideration of seasonal ARIMA models with and without exogenous regressors.
- Simple API.
- GUI access through the gretl menu.
- Public convenience functions for the user for summarizing results, or retrieving the gretl ARIMA command of the 'best' model.
- Source code is tested by means of unit-tests to minimize bugs.
