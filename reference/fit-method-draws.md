# Extract posterior draws

Extract draws or point estimates from fitted model objects using formats
provided by the posterior package. Depending on the fitting method,
these are posterior draws from MCMC, approximate posterior draws from
variational inference, Laplace approximation, or Pathfinder, standalone
generated quantities, or a point estimate from optimization.

The variables include the parameters, transformed parameters, and
generated quantities from the Stan program as well as `lp__`, the target
log density evaluated by Stan, up to an additive constant. See
[`$lp()`](https://mc-stan.org/cmdstanr/reference/fit-method-lp.md) for
details.

## Usage

``` r
draws(
  variables = NULL,
  inc_warmup = FALSE,
  format = getOption("cmdstanr_draws_format")
)
```

## Arguments

- variables:

  (character vector) Optionally, the names of the variables (parameters,
  transformed parameters, and generated quantities) to read in.

  - If `NULL` (the default) then all variables are included.

  - If an empty string (`variables=""`) then none are included.

  - For non-scalar variables all elements or specific elements can be
    selected:

    - `variables = "theta"` selects all elements of `theta`;

    - `variables = c("theta[1]", "theta[3]")` selects only the 1st and
      3rd elements.

- inc_warmup:

  (logical) Should warmup draws be included? Defaults to `FALSE`.
  Ignored except when used with
  [CmdStanMCMC](https://mc-stan.org/cmdstanr/reference/CmdStanMCMC.md)
  objects.

- format:

  (string) The format of the returned draws or point estimates. Must be
  a valid format from the posterior package. The defaults are the
  following.

  - For sampling and generated quantities the default is
    [`"draws_array"`](https://mc-stan.org/posterior/reference/draws_array.html).
    This format keeps the chains separate. To combine the chains use any
    of the other formats (e.g. `"draws_matrix"`).

  - For point estimates from optimization and approximate draws from
    variational inference, Laplace approximation, and Pathfinder the
    default is
    [`"draws_matrix"`](https://mc-stan.org/posterior/reference/draws_matrix.html).

  To use a different format it can be specified as the full name of the
  format from the posterior package (e.g. `format = "draws_df"`) or
  omitting the `"draws_"` prefix (e.g. `format = "df"`).

  **Changing the default format**: To change the default format for an
  entire R session use `options(cmdstanr_draws_format = format)`, where
  `format` is the name (in quotes) of a valid format from the posterior
  package. For example `options(cmdstanr_draws_format = "draws_df")`
  will change the default to a data frame.

  **Note about efficiency**: for sampling and generated quantities,
  `"draws_array"` is the format the draws are read into, so it is the
  cheapest in time and memory. The other formats are converted from it
  and need at least one more copy of the draws, `"draws_list"` and
  `"draws_df"` the most. To save memory read only the variables you need
  with the `variables` argument.

## Value

Depends on the value of `format`. The defaults are:

- For
  [MCMC](https://mc-stan.org/cmdstanr/reference/model-method-sample.md),
  a 3-D
  [`draws_array`](https://mc-stan.org/posterior/reference/draws_array.html)
  object (iteration x chain x variable).

- For standalone [generated
  quantities](https://mc-stan.org/cmdstanr/reference/model-method-generate-quantities.md),
  a 3-D
  [`draws_array`](https://mc-stan.org/posterior/reference/draws_array.html)
  object (iteration x chain x variable).

- For [variational
  inference](https://mc-stan.org/cmdstanr/reference/model-method-variational.md)
  and [Laplace
  approximation](https://mc-stan.org/cmdstanr/reference/model-method-laplace.md),
  a 2-D
  [`draws_matrix`](https://mc-stan.org/posterior/reference/draws_matrix.html)
  object (draw x variable). An additional variable `lp_approx__`
  containing the log density of the corresponding approximation is also
  included.

- For
  [Pathfinder](https://mc-stan.org/cmdstanr/reference/model-method-pathfinder.md),
  a 2-D
  [`draws_matrix`](https://mc-stan.org/posterior/reference/draws_matrix.html)
  object (draw x variable). Additional variables `lp_approx__` and
  `path__` are also included, with `path__` identifying the path
  associated with each draw.

- For
  [optimization](https://mc-stan.org/cmdstanr/reference/model-method-optimize.md),
  a 1-row
  [`draws_matrix`](https://mc-stan.org/posterior/reference/draws_matrix.html)
  with one column per variable. These are *not* actually draws, just
  point estimates stored in the `draws_matrix` format. See
  [`$mle()`](https://mc-stan.org/cmdstanr/reference/fit-method-mle.md)
  to extract them as a numeric vector.

## Examples

``` r
# \dontrun{
# logistic regression with intercept alpha and coefficients beta
fit <- cmdstanr_example("logistic", method = "sample")

# returned as 3-D array (see ?posterior::draws_array)
draws <- fit$draws()
dim(draws)
#> [1] 1000    4  105
str(draws)
#>  'draws_array' num [1:1000, 1:4, 1:105] -64.3 -68.4 -68 -69.9 -67.5 ...
#>  - attr(*, "dimnames")=List of 3
#>   ..$ iteration: chr [1:1000] "1" "2" "3" "4" ...
#>   ..$ chain    : chr [1:4] "1" "2" "3" "4"
#>   ..$ variable : chr [1:105] "lp__" "alpha" "beta[1]" "beta[2]" ...

# can easily convert to other formats (data frame, matrix, list)
# using the posterior package
head(posterior::as_draws_matrix(draws))
#> # A draws_matrix: 6 iterations, 1 chains, and 105 variables
#>     variable
#> draw lp__    alpha beta[1] beta[2] beta[3] log_lik[1] log_lik[2] log_lik[3]
#>    1  -64  0.27903   -0.65  -0.424    0.59      -0.56      -0.42      -0.66
#>    2  -68 -0.11212   -0.47  -0.003    0.25      -0.69      -0.40      -0.55
#>    3  -68  0.90766   -0.77  -0.410    1.11      -0.35      -0.37      -0.31
#>    4  -70  0.88191   -1.02  -0.797    0.84      -0.35      -0.53      -0.59
#>    5  -67 -0.00092   -0.57  -0.034    0.18      -0.62      -0.46      -0.52
#>    6  -65  0.61191   -0.89  -0.241    0.82      -0.40      -0.35      -0.30
#> # ... with 97 more variables

# or can specify 'format' argument to avoid manual conversion
# matrix format combines all chains
draws <- fit$draws(format = "matrix")
head(draws)
#> # A draws_matrix: 6 iterations, 1 chains, and 105 variables
#>     variable
#> draw lp__    alpha beta[1] beta[2] beta[3] log_lik[1] log_lik[2] log_lik[3]
#>    1  -64  0.27903   -0.65  -0.424    0.59      -0.56      -0.42      -0.66
#>    2  -68 -0.11212   -0.47  -0.003    0.25      -0.69      -0.40      -0.55
#>    3  -68  0.90766   -0.77  -0.410    1.11      -0.35      -0.37      -0.31
#>    4  -70  0.88191   -1.02  -0.797    0.84      -0.35      -0.53      -0.59
#>    5  -67 -0.00092   -0.57  -0.034    0.18      -0.62      -0.46      -0.52
#>    6  -65  0.61191   -0.89  -0.241    0.82      -0.40      -0.35      -0.30
#> # ... with 97 more variables

# can select specific parameters
fit$draws("alpha")
#> # A draws_array: 1000 iterations, 4 chains, and 1 variables
#> , , variable = alpha
#> 
#>          chain
#> iteration        1      2      3      4
#>         1  0.27903 0.1707 0.5327  0.551
#>         2 -0.11212 0.2810 0.4444  0.581
#>         3  0.90766 0.3110 0.3907 -0.023
#>         4  0.88191 0.5285 0.0039  0.303
#>         5 -0.00092 0.0067 0.0072  0.362
#> 
#> # ... with 995 more iterations
fit$draws("beta")  # selects entire vector beta
#> # A draws_array: 1000 iterations, 4 chains, and 3 variables
#> , , variable = beta[1]
#> 
#>          chain
#> iteration     1     2     3     4
#>         1 -0.65 -0.47 -0.86 -0.50
#>         2 -0.47 -0.93 -0.54 -0.61
#>         3 -0.77 -0.73 -0.79 -0.70
#>         4 -1.02 -0.76 -1.15 -0.77
#>         5 -0.57 -0.66 -1.22 -0.65
#> 
#> , , variable = beta[2]
#> 
#>          chain
#> iteration      1       2      3      4
#>         1 -0.424 -0.0092 -0.016 -0.128
#>         2 -0.003 -0.4956 -0.354 -0.496
#>         3 -0.410 -0.3112 -0.526 -0.019
#>         4 -0.797 -0.4036 -0.147 -0.237
#>         5 -0.034 -0.1907 -0.153 -0.167
#> 
#> , , variable = beta[3]
#> 
#>          chain
#> iteration    1    2    3    4
#>         1 0.59 0.76 0.42 0.70
#>         2 0.25 0.25 0.67 0.42
#>         3 1.11 0.22 0.62 0.78
#>         4 0.84 0.94 0.97 0.48
#>         5 0.18 0.43 0.76 0.85
#> 
#> # ... with 995 more iterations
fit$draws(c("alpha", "beta[2]"))
#> # A draws_array: 1000 iterations, 4 chains, and 2 variables
#> , , variable = alpha
#> 
#>          chain
#> iteration        1      2      3      4
#>         1  0.27903 0.1707 0.5327  0.551
#>         2 -0.11212 0.2810 0.4444  0.581
#>         3  0.90766 0.3110 0.3907 -0.023
#>         4  0.88191 0.5285 0.0039  0.303
#>         5 -0.00092 0.0067 0.0072  0.362
#> 
#> , , variable = beta[2]
#> 
#>          chain
#> iteration      1       2      3      4
#>         1 -0.424 -0.0092 -0.016 -0.128
#>         2 -0.003 -0.4956 -0.354 -0.496
#>         3 -0.410 -0.3112 -0.526 -0.019
#>         4 -0.797 -0.4036 -0.147 -0.237
#>         5 -0.034 -0.1907 -0.153 -0.167
#> 
#> # ... with 995 more iterations

# can be passed directly to bayesplot plotting functions
bayesplot::color_scheme_set("brightblue")
bayesplot::mcmc_dens(fit$draws(c("alpha", "beta")))

bayesplot::mcmc_scatter(fit$draws(c("beta[1]", "beta[2]")), alpha = 0.3)



# example using variational inference
fit <- cmdstanr_example("logistic", method = "variational")
head(fit$draws("beta")) # a matrix by default
#> # A draws_matrix: 6 iterations, 1 chains, and 3 variables
#>     variable
#> draw beta[1] beta[2] beta[3]
#>    1   -0.56   -0.39    0.54
#>    2   -0.49   -0.21    0.90
#>    3   -0.88   -0.33    0.58
#>    4   -0.88   -0.19    0.31
#>    5   -0.70   -0.36    0.32
#>    6   -0.95   -0.23    0.56
head(fit$draws("beta", format = "df"))
#> # A draws_df: 6 iterations, 1 chains, and 3 variables
#>   beta[1] beta[2] beta[3]
#> 1   -0.56   -0.39    0.54
#> 2   -0.49   -0.21    0.90
#> 3   -0.88   -0.33    0.58
#> 4   -0.88   -0.19    0.31
#> 5   -0.70   -0.36    0.32
#> 6   -0.95   -0.23    0.56
#> # ... hidden reserved variables {'.chain', '.iteration', '.draw'}
# }
```
