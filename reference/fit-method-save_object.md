# Save fitted model object to a file

This method calls
[`$materialize()`](https://mc-stan.org/cmdstanr/reference/fit-method-materialize.md)
internally to ensure that all posterior draws and diagnostics are saved
when saving a fitted model object. Because the contents of the CmdStan
output CSV files are only read into R lazily (i.e., as needed), the
`$save_object()` method is the safest way to guarantee that everything
has been read in before saving.

By default objects are saved using
[`base::saveRDS()`](https://rdrr.io/r/base/readRDS.html). If you have a
big object to save, we recommend setting `format = "qs2"`, which is
faster and more memory efficient. Internally this will use
[`qs2::qs_save()`](https://rdrr.io/pkg/qs2/man/qs_save.html) to save the
object and you can read it back into R later with
[`qs2::qs_read()`](https://rdrr.io/pkg/qs2/man/qs_read.html).

## Usage

``` r
save_object(file, format = c("rds", "qs2"), ...)
```

## Arguments

- file:

  (string) Path where the file should be saved.

- format:

  (string) Serialization format for the object. The default is `"rds"`.
  The `"qs2"` format uses
  [`qs2::qs_save()`](https://rdrr.io/pkg/qs2/man/qs_save.html) and
  requires the qs2 package.

- ...:

  Other arguments to pass to
  [`base::saveRDS()`](https://rdrr.io/r/base/readRDS.html) (for
  `format = "rds"`) or
  [`qs2::qs_save()`](https://rdrr.io/pkg/qs2/man/qs_save.html) (for
  `format = "qs2"`).

## Value

The fitted model object, invisibly.

## See also

[`materialize`](https://mc-stan.org/cmdstanr/reference/fit-method-materialize.md)

## Examples

``` r
# \dontrun{
fit <- cmdstanr_example("logistic")

# using default format = "rds"
temp_rds_file <- tempfile(fileext = ".rds")
fit$save_object(file = temp_rds_file)
rm(fit)

fit <- readRDS(temp_rds_file)
fit$summary()
#> # A tibble: 105 × 10
#>    variable      mean  median     sd    mad       q5     q95  rhat ess_bulk
#>    <chr>        <dbl>   <dbl>  <dbl>  <dbl>    <dbl>   <dbl> <dbl>    <dbl>
#>  1 lp__       -66.0   -65.7   1.45   1.22   -68.9    -64.3   1.000    1988.
#>  2 alpha        0.381   0.381 0.221  0.218    0.0229   0.742 1.000    4372.
#>  3 beta[1]     -0.666  -0.661 0.255  0.257   -1.10    -0.259 1.00     3929.
#>  4 beta[2]     -0.272  -0.274 0.226  0.227   -0.650    0.104 1.00     3863.
#>  5 beta[3]      0.684   0.679 0.270  0.273    0.252    1.13  1.00     4203.
#>  6 log_lik[1]  -0.515  -0.511 0.0983 0.0973  -0.684   -0.362 1.00     3878.
#>  7 log_lik[2]  -0.403  -0.382 0.151  0.145   -0.678   -0.192 1.000    5108.
#>  8 log_lik[3]  -0.496  -0.465 0.216  0.209   -0.899   -0.204 0.999    4210.
#>  9 log_lik[4]  -0.451  -0.429 0.156  0.148   -0.744   -0.233 1.000    3736.
#> 10 log_lik[5]  -1.19   -1.17  0.284  0.279   -1.69    -0.764 1.00     4827.
#> # ℹ 95 more rows
#> # ℹ 1 more variable: ess_tail <dbl>

# using format = "qs2"
temp_qs2_file <- tempfile(fileext = ".qs2")
fit$save_object(file = temp_qs2_file, format = "qs2")
rm(fit)

fit <- qs2::qs_read(temp_qs2_file)
fit$summary()
#> # A tibble: 105 × 10
#>    variable      mean  median     sd    mad       q5     q95  rhat ess_bulk
#>    <chr>        <dbl>   <dbl>  <dbl>  <dbl>    <dbl>   <dbl> <dbl>    <dbl>
#>  1 lp__       -66.0   -65.7   1.45   1.22   -68.9    -64.3   1.000    1988.
#>  2 alpha        0.381   0.381 0.221  0.218    0.0229   0.742 1.000    4372.
#>  3 beta[1]     -0.666  -0.661 0.255  0.257   -1.10    -0.259 1.00     3929.
#>  4 beta[2]     -0.272  -0.274 0.226  0.227   -0.650    0.104 1.00     3863.
#>  5 beta[3]      0.684   0.679 0.270  0.273    0.252    1.13  1.00     4203.
#>  6 log_lik[1]  -0.515  -0.511 0.0983 0.0973  -0.684   -0.362 1.00     3878.
#>  7 log_lik[2]  -0.403  -0.382 0.151  0.145   -0.678   -0.192 1.000    5108.
#>  8 log_lik[3]  -0.496  -0.465 0.216  0.209   -0.899   -0.204 0.999    4210.
#>  9 log_lik[4]  -0.451  -0.429 0.156  0.148   -0.744   -0.233 1.000    3736.
#> 10 log_lik[5]  -1.19   -1.17  0.284  0.279   -1.69    -0.764 1.00     4827.
#> # ℹ 95 more rows
#> # ℹ 1 more variable: ess_tail <dbl>
# }
```
