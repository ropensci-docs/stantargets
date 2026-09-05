# Write an example Stan model file.

Overwrites the file at `path` with a built-in example Stan model file.

## Usage

``` r
tar_stan_example_file(path = tempfile(pattern = "", fileext = ".stan"))
```

## Arguments

- path:

  Character of length 1, file path to write the model file.

## Value

`NULL` (invisibly).

## See also

Other examples:
[`tar_stan_example_data()`](https://docs.ropensci.org/stantargets/reference/tar_stan_example_data.md)

## Examples

``` r
path <- tempfile(pattern = "", fileext = ".stan")
tar_stan_example_file(path = path)
writeLines(readLines(path))
#> data {
#>   int <lower = 1> n;
#>   vector[n] x;
#>   vector[n] y;
#> }
#> parameters {
#>   real beta;
#> }
#> model {
#>   y ~ normal(x * beta, 1);
#>   beta ~ normal(0, 1);
#> }
#> generated quantities {
#>   array[n] real y_rep = normal_rng(x * beta, 1);
#> }
```
