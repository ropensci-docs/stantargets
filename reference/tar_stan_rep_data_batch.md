# Generate a batch of data

Not a user-side function. Do not invoke directly.

## Usage

``` r
tar_stan_rep_data_batch(reps, batch, command)
```

## Arguments

- reps:

  Positive integer of length 1, number of reps to run.

- batch:

  Positive integer of length 1, index of the current batch.

- command:

  R code to run to generate one dataset.

## Value

A list of Stan datasets containing data and dataset IDs.

## Examples

``` r
tar_stan_rep_data_batch(2, 1, tar_stan_example_data())
#> [[1]]
#> [[1]]$n
#> [1] 10
#> 
#> [[1]]$x
#>  [1] -1.0000000 -0.7777778 -0.5555556 -0.3333333 -0.1111111  0.1111111
#>  [7]  0.3333333  0.5555556  0.7777778  1.0000000
#> 
#> [[1]]$y
#>  [1] -1.1608308  0.7161246  0.1925700  0.2711203  1.2984119  0.6999397
#>  [7]  0.1750891  1.1362843  1.6416750  1.4674945
#> 
#> [[1]]$true_beta
#> [1] 0.4491701
#> 
#> [[1]]$.join_data
#> [[1]]$.join_data$beta
#> [1] 0.4491701
#> 
#> [[1]]$.join_data$y_rep
#>  [1] -1.1608308  0.7161246  0.1925700  0.2711203  1.2984119  0.6999397
#>  [7]  0.1750891  1.1362843  1.6416750  1.4674945
#> 
#> 
#> [[1]]$.dataset_id
#> [1] "target_1"
#> 
#> [[1]]$.seed
#> [1] 570998469
#> 
#> 
#> [[2]]
#> [[2]]$n
#> [1] 10
#> 
#> [[2]]$x
#>  [1] -1.0000000 -0.7777778 -0.5555556 -0.3333333 -0.1111111  0.1111111
#>  [7]  0.3333333  0.5555556  0.7777778  1.0000000
#> 
#> [[2]]$y
#>  [1] -1.86246566 -1.41879468 -0.85307187  0.82649891 -0.72619941  0.16771508
#>  [7] -0.07545352  1.38143632  2.30435616  1.64955975
#> 
#> [[2]]$true_beta
#> [1] 2.177144
#> 
#> [[2]]$.join_data
#> [[2]]$.join_data$beta
#> [1] 2.177144
#> 
#> [[2]]$.join_data$y_rep
#>  [1] -1.86246566 -1.41879468 -0.85307187  0.82649891 -0.72619941  0.16771508
#>  [7] -0.07545352  1.38143632  2.30435616  1.64955975
#> 
#> 
#> [[2]]$.dataset_id
#> [1] "target_2"
#> 
#> [[2]]$.seed
#> [1] 1034106831
#> 
#> 
```
