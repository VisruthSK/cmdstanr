# Compute a summary table of estimates and diagnostics

The `$summary()` method runs
[`summarise_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html)
from the posterior package and returns the output. For MCMC, only
post-warmup draws are included in the summary.

There is also a `$print()` method that prints the same summary stats but
removes the extra formatting used for printing tibbles and returns the
fitted model object itself. The `$print()` method may also be faster
than `$summary()` because it is designed to only compute the summary
statistics for the variables that will actually fit in the printed
output whereas `$summary()` will compute them for all of the specified
variables in order to be able to return them to the user. The `$print()`
method accepts the same `variables` and `...` arguments as `$summary()`.
It also has a `digits` argument for the number of digits to display
after the decimal point (default `2`) and a `max_rows` argument for the
maximum number of rows to print (default
`getOption("cmdstanr_max_rows", 10)`). See **Examples**.

## Usage

``` r
summary(variables = NULL, ...)
```

## Arguments

- variables:

  (character vector) The variables to include.

- ...:

  Optional arguments to pass to
  [`posterior::summarise_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html).

## Value

The `$summary()` method returns the tibble data frame created by
[`posterior::summarise_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html).

The `$print()` method returns the fitted model object itself
(invisibly), which is the standard behavior for print methods in R.

## References

- Vehtari, A., Gelman, A., Simpson, D., Carpenter, B., and Buerkner,
  P.-C. (2021). Rank-normalization, folding, and localization: An
  improved R-hat for assessing convergence of MCMC (with discussion).
  *Bayesian Analysis*, 16(2), 667-718. doi:10.1214/20-BA1221.

- Vehtari, A. (2021). Comparison of MCMC effective sample size
  estimators. https://avehtari.github.io/rhat_ess/ess_comparison.html
  (for ESS diagnostics such as `ess_bulk` and `ess_tail`).

## Examples

``` r
# \dontrun{
fit <- cmdstanr_example("logistic")
fit$summary()
#> # A tibble: 105 × 10
#>    variable      mean  median     sd    mad       q5      q95  rhat ess_bulk
#>    <chr>        <dbl>   <dbl>  <dbl>  <dbl>    <dbl>    <dbl> <dbl>    <dbl>
#>  1 lp__       -65.9   -65.6   1.42   1.21   -68.8    -64.3    1.00     2081.
#>  2 alpha        0.379   0.380 0.213  0.207    0.0310   0.729  1.000    4425.
#>  3 beta[1]     -0.668  -0.659 0.241  0.234   -1.08    -0.288  1.00     3945.
#>  4 beta[2]     -0.276  -0.271 0.228  0.222   -0.654    0.0948 1.00     3932.
#>  5 beta[3]      0.677   0.666 0.269  0.265    0.256    1.14   1.00     3911.
#>  6 log_lik[1]  -0.515  -0.508 0.0965 0.0941  -0.680   -0.366  1.00     4159.
#>  7 log_lik[2]  -0.404  -0.385 0.144  0.139   -0.665   -0.200  1.00     4142.
#>  8 log_lik[3]  -0.500  -0.465 0.219  0.204   -0.912   -0.205  1.00     4093.
#>  9 log_lik[4]  -0.450  -0.428 0.153  0.152   -0.728   -0.238  1.000    3723.
#> 10 log_lik[5]  -1.18   -1.16  0.280  0.276   -1.66    -0.756  1.00     4494.
#> # ℹ 95 more rows
#> # ℹ 1 more variable: ess_tail <dbl>
fit$print()
#>    variable   mean median   sd  mad     q5    q95 rhat ess_bulk ess_tail
#>  lp__       -65.91 -65.56 1.42 1.21 -68.78 -64.28 1.00     2081     2817
#>  alpha        0.38   0.38 0.21 0.21   0.03   0.73 1.00     4425     3147
#>  beta[1]     -0.67  -0.66 0.24 0.23  -1.08  -0.29 1.00     3945     3011
#>  beta[2]     -0.28  -0.27 0.23 0.22  -0.65   0.09 1.00     3932     2973
#>  beta[3]      0.68   0.67 0.27 0.26   0.26   1.14 1.00     3911     2544
#>  log_lik[1]  -0.52  -0.51 0.10 0.09  -0.68  -0.37 1.00     4159     3106
#>  log_lik[2]  -0.40  -0.39 0.14 0.14  -0.66  -0.20 1.00     4141     3162
#>  log_lik[3]  -0.50  -0.47 0.22 0.20  -0.91  -0.21 1.00     4093     3258
#>  log_lik[4]  -0.45  -0.43 0.15 0.15  -0.73  -0.24 1.00     3722     2748
#>  log_lik[5]  -1.18  -1.16 0.28 0.28  -1.66  -0.76 1.00     4494     3057
#> 
#>  # showing 10 of 105 rows (change via `max_rows` argument or `cmdstanr_max_rows` option)
fit$print(max_rows = 2) # same as print(fit, max_rows = 2)
#>  variable   mean median   sd  mad     q5    q95 rhat ess_bulk ess_tail
#>     lp__  -65.91 -65.56 1.42 1.21 -68.78 -64.28 1.00     2081     2817
#>     alpha   0.38   0.38 0.21 0.21   0.03   0.73 1.00     4425     3147
#> 
#>  # showing 2 of 105 rows (change via `max_rows` argument or `cmdstanr_max_rows` option)

# include only certain variables
fit$summary("beta")
#> # A tibble: 3 × 10
#>   variable   mean median    sd   mad     q5     q95  rhat ess_bulk ess_tail
#>   <chr>     <dbl>  <dbl> <dbl> <dbl>  <dbl>   <dbl> <dbl>    <dbl>    <dbl>
#> 1 beta[1]  -0.668 -0.659 0.241 0.234 -1.08  -0.288   1.00    3945.    3012.
#> 2 beta[2]  -0.276 -0.271 0.228 0.222 -0.654  0.0948  1.00    3932.    2974.
#> 3 beta[3]   0.677  0.666 0.269 0.265  0.256  1.14    1.00    3911.    2545.
fit$print(c("alpha", "beta[2]"))
#>  variable  mean median   sd  mad    q5  q95 rhat ess_bulk ess_tail
#>   alpha    0.38   0.38 0.21 0.21  0.03 0.73 1.00     4425     3147
#>   beta[2] -0.28  -0.27 0.23 0.22 -0.65 0.09 1.00     3932     2973

# include all variables but only certain summaries
fit$summary(NULL, c("mean", "sd"))
#> # A tibble: 105 × 3
#>    variable      mean     sd
#>    <chr>        <dbl>  <dbl>
#>  1 lp__       -65.9   1.42  
#>  2 alpha        0.379 0.213 
#>  3 beta[1]     -0.668 0.241 
#>  4 beta[2]     -0.276 0.228 
#>  5 beta[3]      0.677 0.269 
#>  6 log_lik[1]  -0.515 0.0965
#>  7 log_lik[2]  -0.404 0.144 
#>  8 log_lik[3]  -0.500 0.219 
#>  9 log_lik[4]  -0.450 0.153 
#> 10 log_lik[5]  -1.18  0.280 
#> # ℹ 95 more rows

# can use functions created from formulas
# for example, calculate Pr(beta > 0)
fit$summary("beta", prob_gt_0 = ~ mean(. > 0))
#> # A tibble: 3 × 2
#>   variable prob_gt_0
#>   <chr>        <dbl>
#> 1 beta[1]     0.0015
#> 2 beta[2]     0.114 
#> 3 beta[3]     0.996 

# can combine user-specified functions with
# the default summary functions
fit$summary(variables = c("alpha", "beta"),
  posterior::default_summary_measures()[1:4],
  quantiles = ~ quantile2(., probs = c(0.025, 0.975)),
  posterior::default_convergence_measures()
  )
#> # A tibble: 4 × 10
#>   variable   mean median    sd   mad    q2.5  q97.5  rhat ess_bulk ess_tail
#>   <chr>     <dbl>  <dbl> <dbl> <dbl>   <dbl>  <dbl> <dbl>    <dbl>    <dbl>
#> 1 alpha     0.379  0.380 0.213 0.207 -0.0394  0.809 1.000    4425.    3148.
#> 2 beta[1]  -0.668 -0.659 0.241 0.234 -1.16   -0.216 1.00     3945.    3012.
#> 3 beta[2]  -0.276 -0.271 0.228 0.222 -0.735   0.160 1.00     3932.    2974.
#> 4 beta[3]   0.677  0.666 0.269 0.265  0.167   1.23  1.00     3911.    2545.

# the functions need to calculate the appropriate
# value for a matrix input
fit$summary(variables = "alpha", dim)
#> # A tibble: 1 × 3
#>   variable dim.1 dim.2
#>   <chr>    <int> <int>
#> 1 alpha     1000     4

# the usual [stats::var()] is therefore not directly suitable as it
# will produce a covariance matrix unless the data is converted to a vector
fit$print(c("alpha", "beta"), var2 = ~var(as.vector(.x)))
#>  variable var2
#>   alpha   0.05
#>   beta[1] 0.06
#>   beta[2] 0.05
#>   beta[3] 0.07

# }
```
