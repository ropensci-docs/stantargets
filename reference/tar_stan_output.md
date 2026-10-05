# Post-process Stan output

Not a user-side function. Do not invoke directly.

## Usage

``` r
tar_stan_output(
  fit,
  output_type,
  summaries,
  summary_args,
  transform,
  variables,
  inc_warmup,
  data,
  data_copy,
  seed
)
```

## Arguments

- fit:

  A Stan fit object.

- output_type:

  Type of output to create, either `"summaries"`, `"draws"`, or
  `"diagnostics"`.

- summaries:

  Optional list of summary functions passed to `...` in
  [`posterior::summarize_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html)
  through `$summary()` on the `CmdStanFit` object.

- summary_args:

  Optional list of summary function arguments passed to `.args` in
  [`posterior::summarize_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html)
  through `$summary()` on the `CmdStanFit` object.

- transform:

  Symbol or `NULL`, name of a function that accepts arguments `data` and
  `draws` and returns a data frame. Here, `data` is the JAGS data list
  supplied to the model, and `draws` is a data frame with one column per
  model parameter and one row per posterior sample. Any arguments other
  than `data` and `draws` must have valid default values because
  `stantargets` will not populate them. See the simulation-based
  calibration (SBC) section of the simulation vignette for an example.

- variables:

  (character vector) The variables to include.

- inc_warmup:

  Logical, whether to include the warmup draws.

- data:

  List, Stan dataset.

- data_copy:

  Character vector of names of scalars in `data`. These values will be
  inserted as columns in the output data frame for each rep. To join
  more than just scalars, include a `.join_data` element of your Stan
  data list with names and dimensions corresponding to those of the
  model. For details, read
  <https://docs.ropensci.org/stantargets/articles/simulation.html>.

- seed:

  Integer vector, random number generator seed used to run Stan.

## Value

A data frame of user-friendly Stan output.
