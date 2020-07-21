# The gretl *auto_arima* package

Returns best SARIMAX model according to either the AIC, the AICc, the BIC or the HQC information criteria. The function conducts a (brute force) search over possible model within the order constraints provided.

This is an alternative package to the existing gretl *armax* package written by Yi-Nung Yang which has some limitations, e.g. it does only work for ARMA type models.

# Features
- Consideration of seasonal ARIMA models with and without exogenous regressors.
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

## Simple example
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

0|0|0      -1022.472        -1022.508        -1017.018        -1020.280   
0|0|1      -1033.926        -1034.033        -1025.798        -1030.691   
.
.
.
4|0|3      -1043.260***     -1044.618***     -1019.914        -1034.591   
4|0|4      -1042.179        -1043.893        -1016.444        -1032.751   
4|1|0      -1015.697        -1016.248         -999.778        -1009.563   
4|1|1      -1031.216        -1031.994        -1012.780        -1024.195   
4|1|2      -1039.927        -1040.974        -1019.015        -1032.061   
4|1|3      -1038.410        -1039.768        -1015.064        -1029.741   
4|1|4      -1036.332        -1038.046        -1010.597        -1026.905   
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
           4            1            4            1            1            1 
```

Lastly, the user can retrieve the gretl command as a string variable for a specific model by means of the ```get_auto_arima_command()``` function. Also this function takes the same three arguments as ```get_auto_arima_parameters()```:
```
# Retrieve the gretl command for estimating the n-th best model according to BIC
string arima_cmd = get_auto_arima_command(model, "bic", 2)
print arima_cmd
```
which returns the string:
```
arima 4 1 4 ; 1 1 0 ; y const  
```

This string can be used for estimating the selected model:
```
# Execute actual estimation
@arima_cmd
```
Calling the built-in gretl ```arima``` command returns the following estimation output:
```
Function evaluations: 155
Evaluations of gradient: 46

Model 1: ARIMA, using observations 1950:02-1960:12 (T = 131)
Estimated using AS 197 (exact ML)
Dependent variable: (1-L)(1-Ls) y
Standard errors based on Hessian

             coefficient    std. error      z       p-value 
  ----------------------------------------------------------
  const       8.07667e-05   0.00136210    0.05930   0.9527  
  phi_1      -0.979926      0.187665     -5.222     1.77e-07 ***
  phi_2      -0.881923      0.174184     -5.063     4.12e-07 ***
  phi_3      -1.01732       0.165473     -6.148     7.85e-10 ***
  phi_4      -0.247282      0.177538     -1.393     0.1637  
  Phi_1      -0.420615      0.0854750    -4.921     8.61e-07 ***
  theta_1     0.586332      0.199142      2.944     0.0032   ***
  theta_2     0.613887      0.183238      3.350     0.0008   ***
  theta_3     0.697329      0.193549      3.603     0.0003   ***
  theta_4    -0.330226      0.200873     -1.644     0.1002  

Mean dependent var   0.000291   S.D. dependent var   0.045848
Mean of innovations  0.000837   S.D. of innovations  0.034423
R-squared            0.992497   Adjusted R-squared   0.992005
Log-likelihood       249.9932   Akaike criterion    -477.9865
Schwarz criterion   -446.3593   Hannan-Quinn        -465.1350

                        Real  Imaginary    Modulus  Frequency
  -----------------------------------------------------------
  AR
    Root  1          -1.0980     0.0000     1.0980     0.5000
    Root  2           0.1323    -1.0513     1.0596    -0.2301
    Root  3           0.1323     1.0513     1.0596     0.2301
    Root  4          -3.2805     0.0000     3.2805     0.5000
  AR (seasonal)
    Root  1          -2.3775     0.0000     2.3775     0.5000
  MA
    Root  1          -1.0000     0.0000     1.0000     0.5000
    Root  2           0.0417     0.9991     1.0000     0.2434
    Root  3           0.0417    -0.9991     1.0000    -0.2434
    Root  4           3.0282     0.0000     3.0282     0.0000
  -----------------------------------------------------------
```
