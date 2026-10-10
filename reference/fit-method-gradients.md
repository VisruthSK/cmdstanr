# Extract gradients after diagnostic mode

Return the data frame containing the gradients for all parameters.

## Usage

``` r
gradients()
```

## Value

A data frame containing the gradients for all parameters.

## See also

[`CmdStanDiagnose`](https://mc-stan.org/cmdstanr/reference/CmdStanDiagnose.md)

## Examples

``` r
# \dontrun{
test <- cmdstanr_example("logistic", method = "diagnose")

# retrieve the gradients
test$gradients()
#>   param_idx      value     model finite_diff       error
#> 1         0  0.3363540   2.48996     2.48996 3.87843e-09
#> 2         1 -0.0802430 -13.06710   -13.06710 6.41193e-09
#> 3         2  0.9223560 -25.46110   -25.46110 1.03328e-08
#> 4         3  0.0627194   6.89051     6.89051 5.50138e-12
# }
```
