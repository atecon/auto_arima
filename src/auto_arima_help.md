# auto_arima 

Fit best SARIMA(X) model to univariate time series.

Returns best SARIMA(X) model according to either AIC, AICc, BIC, or HQC value. The function conducts a brute-force search over possible models within the order constraints provided.

The *auto_arima* has the following features:

- Support for seasonal ARIMA models with/without exogenous regressors
- GUI access through gretl menu
- Public convenience functions for result summarization and retrieving the gretl ARIMA command of the 'best' model.
- Source code is tested by means of unit-tests to minimize bugs.

Source code and test scripts: https://github.com/atecon/auto_arima

This package is an extension of gretl's built-in `arima` command for which the  degrees of differencing in the command are not treated as subject to search:

https://gretl.sourceforge.net/gretl-help/cmdref.html#arima

Furthermore, the *auto_arima* package is also an alternative to the *armax* package written by Yi-Nung Yang which has some limitations, e.g. it does only work for ARMA type models.


## GUI Access

Access via: Model -> Univariate time series -> Automatic ARIMA

**Seasonality Settings**:

If at least one of the following options is non-zero, the function will consider seasonal parameters:

- `max_P`: Maximum seasonal autoregressive order
- `max_D`: Maximum seasonal-differencing order
- `max_Q`: Maximum seasonal moving-average order

Non-seasonal ARIMA(X) considered if these seasonality parameters are zero or dataset is non-seasonal.


# Public Functions

```
auto_arima (series y, list xlist, bundle opts)
```

Fit SARIMA(X) model to endogenous series `y`.

## Parameters

- `y`: series - Endogenous variable
- `xlist`: list - Exogenous variables (optional)
- `opts`: bundle - SARIMA parameters and options (see below)

## Returns

Bundle containing model results and diagnostics.

---

```
print_auto_arima_results (bundle model)
```

Print summary results for parameter combinations and information criteria. In case model estimation fails for some parameter setting (e.g. due to non-convergence), NA is returned for this setting.


## Parameters

- `model`: bundle - Result from `auto_arima()`

---

```
get_auto_arima_parameters (bundle model, string info_criteria, int model_rank[1::])
```

Retrieve SARIMA parameters for n-th best model by information criterion.

## Parameters

- `model`: bundle - Result from `auto_arima()`
- `info_criteria`: string - "aicc", "aic", "bic", or "hqc"
- `model_rank`: int - Ranking position (1 = best)

## Returns

Vector of SARIMA parameters.

---

```
get_auto_arima_command (bundle model, string info_criteria, int model_rank[1::])
```

Generate hansl command for specified model.

## Parameters

- `model`: bundle - Result from `auto_arima()`
- `info_criteria`: string - "aicc", "aic", "bic", or "hqc"
- `model_rank`: int - Ranking position (1 = best)

## Returns

String containing executable hansl command.

# Options Bundle (opts)

Supported parameters:

- `verbose`: bool - Enable detailed output (default 1)
- `with_intercept`: bool - Include intercept term (default 1)
- `with_seasonality`: bool - Consider seasonal parameters (default 1)
- `estimation_method`: string - "conditional_ml" for conditional ML or "exact_ml" for exact ML (default: "exact_ml")
- SARIMA order limits (see defaults below)

# Default SARIMA Parameters

- `min_p`/`max_p`: 0/4 (AR orders)
- `min_d`/`max_d`: 0/1 (Differencing)
- `min_q`/`max_q`: 0/4 (MA orders)  
- `min_P`/`max_P`: 0/1 (Seasonal AR)
- `min_D`/`max_D`: 0/1 (Seasonal diff)
- `min_Q`/`max_Q`: 0/1 (Seasonal MA)

# Changelog

* **v0.9 (April 2025)**
    * Add conditional/exact ML estimation option
    * Improve GUI documentation
    * Update help content and switch to markdown format

* **v0.8 (February 2023)**
    * Remove line breaks in help text
    * Fix typos

* **v0.7 (September 2020)**
    * Fix error handling with `verbose=0`
    * Set default `verbose` to 0

* **v0.6 (August 2020)**
    * Fix model ranking bugs
    * Improve error handling
    * Enhance unit tests

* **v0.52 (August 2020)**
    * Fix `max_P` handling in GUI

* **v0.51 (July 2020)**
    * Fix command generation bugs
    * Automate seasonality detection

* **v0.5 (July 2020)**
    * Initial release
