# Join some Stan data to summary output

Not a user-side function. Do not invoke directly.

## Usage

``` r
tar_stan_summary_join_data(summaries, data)
```

## Arguments

- summaries:

  A data frame of Stan posterior summaries.

- data:

  Code to generate the `data` for the Stan model.

## Value

A data frame of user-friendly Stan output.
