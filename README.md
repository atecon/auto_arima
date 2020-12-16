# The gretl *auto_arima* package
[![Build Status](https://travis-ci.org/atecon/string_utils.svg?branch=master)](https://travis-ci.org/atecon/auto_arima)

Returns best SARIMAX model according to either the AIC, the AICc, the BIC or the HQC information criteria. The function conducts a (brute force) search over possible model within the order constraints provided.

This is an alternative package to the existing gretl *armax* package written by Yi-Nung Yang which has some limitations, e.g. it does only work for ARMA type models.

# Features
- Consideration of seasonal ARIMA models with and without exogenous regressors.
- Fine tuning the parameter space to evaluate.
- Simple API.
- GUI access through the gretl menu.
- Public convenience functions for the user for summarizing results, or retrieving the gretl ARIMA command of the 'best' model.
- Source code is tested by means of unit-tests to minimize bugs.

# Detailed help file
A detailed help file can be found here: https://raw.githubusercontent.com/atecon/auto_arima/master/src/auto_arima_help.txt

# Installation and usage
Get the package from the gretl package server and install it:
```
pkg install auto_arima
```
## GUI interface
Once the package is installed, the user can access the GUI interface via the "Model --> Univariate time series --> Automatic ARIMA" menu. The interface will look like this:

![sample](https://github.com/atecon/auto_arima/raw/master/gui.png)


## Simple scripting example
Here is a sample script on how to use it (see also: https://raw.githubusercontent.com/atecon/auto_arima/master/src/auto_arima_sample.inp):

First, load the package and open a monthly time-series data set. In this example, we pass only the endogenous series ```lg``` (144 observations) to the main function ```auto_arima```. However, the interested user can also pass a list of exogenous variables as the 2nd argument to ```auto_arima```.

This function executes all the computation and estimates several seasonal ARIMA models. All relevant output is stored in the returned bundle (a kind of struct data type) named ```model```:
```
clear
set verbose off
include auto_arima.gfn

# Box and Jenkins "Series G" (airline passengers)
open bjg.gdt -p -q         # monthly frequency

# Find "best" ARIMA model and pass some additional option
bundle model = auto_arima(lg)
```
Estimating and evaluating 400 different ARIMA models takes only about 4 seconds on a 7 years old i7 laptop.

Once the computation is done, the user can print the summary results by means of the ```print_auto_arima_results()``` function. This returns for all ARIMA
```
print_auto_arima_results(model)
```
The printed table looks like this (only an extraction is shown):
```
***************************************************************************
                Summary results of ARIMA model combinations

Three, two and one asterisk(s) below indicate the 1st, 2nd and 3rd best (that
is, minimized) values of the respective information criteria, AIC = Akaike
criterion, AICC = corrected AIC, BIC = Schwarz Bayesian criterion and
HQC = Hannan-Quinn criterion across all ARIMA model specification.

Common sample used for all models: 1877 to 1991

ARIMA-spec.     aicc            aic             bic             hqc
---------------------------------------------------------------------------

0|0|0 |0|0|0   164.084          163.996          172.821        167.582
0|0|0 |0|0|1   39.974           39.798           51.564         44.580
.
.
.
2|1|1 |0|1|1   -481.613         -482.290         -462.164         -474.112
2|1|1 |1|0|0   -463.112         -463.743         -443.152         -455.375
2|1|1 |1|0|1   -488.421*        -489.269**       -465.736         -479.706*
2|1|1 |1|1|0   -471.535         -472.213         -452.086         -464.034
.
.
2|1|3 |1|0|1   -488.500**       -489.885***      -460.468         -477.931
2|1|3 |1|1|0   -477.911         -479.092         -453.215         -468.577
2|1|3 |1|1|1   -480.705         -482.192         -453.440         -470.509
.
.
.
***************************************************************************
```

Another useful public function is ```get_auto_arima_parameters()```. It takes as arguments the model bundle, a string referring to some information criteria and a positive integer referring to the n-th best model according to this information criteria. The function prints a row vector with the ARIMA parameters of this selected model:

```
# Print coefficient vector of the n-th best model using the AIC
matrix arima_params = get_auto_arima_parameters(model, "aic", 1)
print arima_params
```
The output is:
```
arima_params (1 x 6)

           p            d            q            P            D            Q
           2            1            3            1            0            1
```

Lastly, the user can retrieve the gretl command as a string variable for a specific model by means of the ```get_auto_arima_command()``` function. Also this function takes the same three arguments as ```get_auto_arima_parameters()```:
```
# Retrieve the gretl command for estimating the n-th best model according to BIC
string arima_cmd = get_auto_arima_command(model, "bic", 2)
print arima_cmd
```
which returns the string:
```
arima 2 0 0 ; 0 1 1 ; lg const L
```

This string can be used for estimating the selected model:
```
# Execute actual estimation
@arima_cmd
```
Calling the built-in gretl ```arima``` command returns the following estimation output:
```
Function evaluations: 81
Evaluations of gradient: 20

Model 1: ARMAX, using observations 1950:01-1960:12 (T = 132)
Estimated using AS 197 (exact ML)
Dependent variable: (1-Ls) lg
Standard errors based on Hessian

             coefficient   std. error     z      p-value
  -------------------------------------------------------
  const       0.118604     0.00904671   13.11    2.88e-39 ***
  phi_1       0.590424     0.0853470     6.918   4.58e-12 ***
  phi_2       0.246938     0.0853191     2.894   0.0038   ***
  Theta_1    -0.558276     0.0773696    -7.216   5.37e-13 ***
  foo        -0.00301776   0.00239775   -1.259   0.2082

Mean dependent var   0.119822   S.D. dependent var   0.061645
Mean of innovations  0.000677   S.D. of innovations  0.035567
R-squared            0.992258   Adjusted R-squared   0.992077
Log-likelihood       250.4308   Akaike criterion    -488.8616
Schwarz criterion   -471.5648   Hannan-Quinn        -481.8329

                        Real  Imaginary    Modulus  Frequency
  -----------------------------------------------------------
  AR
    Root  1           1.1452     0.0000     1.1452     0.0000
    Root  2          -3.5362     0.0000     3.5362     0.5000
  MA (seasonal)
    Root  1           1.7912     0.0000     1.7912     0.0000
  -----------------------------------------------------------

```


# Changelog

### v0.6, August 2020
- Fix bug in print_table_and_row_labels(): wrong models where highlighted as best ones.
- Fix bug in get_auto_arima_parameters(): wrong n-th best model was retrieved.
- Fix typos in help file.
- Unit-tests added and improved.

### v0.52, August 2020
- Fix ```max_P``` parameter for GUI wrapper function

### v0.51, July 2020
- Fix bug when executing get_auto_arima_command() function: Names of endogenous series and exogenous list where not correct in every case.
- GUI access: Boolean parameter "seasonality" dropped: It is automatically checked now whether the active data set has seasonal frequency.
- Minor formatting improvements.

### v0.5, July 2020
- initial release
