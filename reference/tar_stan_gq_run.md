# Compile and run a Stan model and return the `CmdStanFit` object.

Not a user-side function. Do not invoke directly.

## Usage

``` r
tar_stan_gq_run(
  stan_file,
  data,
  fitted_params,
  compile,
  quiet,
  stdout,
  stderr,
  dir,
  pedantic,
  include_paths,
  cpp_options,
  stanc_options,
  force_recompile,
  seed,
  output_dir,
  sig_figs,
  parallel_chains,
  threads_per_chain,
  variables
)
```

## Arguments

- stan_file:

  (string) The path to a `.stan` file containing a Stan program. The
  helper function
  [`write_stan_file()`](https://mc-stan.org/cmdstanr/reference/write_stan_file.html)
  is provided for cases when it is more convenient to specify the Stan
  program as a string. If `stan_file` is not specified then `exe_file`
  must be specified.

- data:

  (multiple options) The data to use for the variables specified in the
  data block of the Stan program. One of the following:

  - A named list of R objects with the names corresponding to variables
    declared in the data block of the Stan program. Internally this list
    is then written to JSON for CmdStan using
    [`write_stan_json()`](https://mc-stan.org/cmdstanr/reference/write_stan_json.html).
    See
    [`write_stan_json()`](https://mc-stan.org/cmdstanr/reference/write_stan_json.html)
    for details on the conversions performed on R objects before they
    are passed to Stan.

  - A path to a data file compatible with CmdStan (JSON or R dump). See
    the appendices in the CmdStan guide for details on using these
    formats.

  - `NULL` or an empty list if the Stan program has no data block.

- fitted_params:

  (multiple options) The parameter draws to use. One of the following:

  - A
    [CmdStanMCMC](https://mc-stan.org/cmdstanr/reference/CmdStanMCMC.html)
    or
    [CmdStanVB](https://mc-stan.org/cmdstanr/reference/CmdStanVB.html)
    fitted model object.

  - A
    [posterior::draws_array](https://mc-stan.org/posterior/reference/draws_array.html)
    (for MCMC) or
    [posterior::draws_matrix](https://mc-stan.org/posterior/reference/draws_matrix.html)
    (for VB) object returned by CmdStanR's
    [`$draws()`](https://mc-stan.org/cmdstanr/reference/fit-method-draws.html)
    method.

  - A character vector of paths to CmdStan CSV output files.

  NOTE: if you plan on making many calls to `$generate_quantities()`
  then the most efficient option is to pass the paths of the CmdStan CSV
  output files (this avoids CmdStanR having to rewrite the draws
  contained in the fitted model object to CSV each time). If you no
  longer have the CSV files you can use
  [`draws_to_csv()`](https://mc-stan.org/cmdstanr/reference/draws_to_csv.html)
  once to write them and then pass the resulting file paths to
  `$generate_quantities()` as many times as needed.

- compile:

  (logical) Do compilation? The default is `TRUE`. If `FALSE`
  compilation can be done later via the
  [`$compile()`](https://mc-stan.org/cmdstanr/reference/model-method-compile.html)
  method.

- quiet:

  (logical) Should the verbose output from CmdStan during compilation be
  suppressed? The default is `TRUE`, but if you encounter an error we
  recommend trying again with `quiet=FALSE` to see more of the output.

- stdout:

  Character of length 1, file path to write the stdout stream of the
  model when it runs. Set to `NULL` to print to the console. Set to
  [`R.utils::nullfile()`](https://henrikbengtsson.github.io/R.utils/reference/nullfile.html)
  to suppress stdout. Does not apply to messages, warnings, or errors.

- stderr:

  Character of length 1, file path to write the stderr stream of the
  model when it runs. Set to `NULL` to print to the console. Set to
  [`R.utils::nullfile()`](https://henrikbengtsson.github.io/R.utils/reference/nullfile.html)
  to suppress stderr. Does not apply to messages, warnings, or errors.

- dir:

  (string) The path to the directory in which to store the CmdStan
  executable (or `.hpp` file if using `$save_hpp_file()`). The default
  is the same location as the Stan program.

- pedantic:

  (logical) Should pedantic mode be turned on? The default is `FALSE`.
  Pedantic mode attempts to warn you about potential issues in your Stan
  program beyond syntax errors. For details see the [*Pedantic mode*
  section](https://mc-stan.org/docs/stan-users-guide/pedantic-mode.html)
  in the Stan Reference Manual. **Note:** to do a pedantic check for a
  model without compiling it or for a model that is already compiled the
  [`$check_syntax()`](https://mc-stan.org/cmdstanr/reference/model-method-check_syntax.html)
  method can be used instead.

- include_paths:

  (character vector) Paths to directories where Stan should look for
  files specified in `#include` directives in the Stan program.

- cpp_options:

  (list) Any makefile options to be used when compiling the model
  (`STAN_THREADS`, `STAN_MPI`, `STAN_OPENCL`, etc.). Anything you would
  otherwise write in the `make/local` file. For an example of using
  threading see the Stan case study [Reduce Sum: A Minimal
  Example](https://mc-stan.org/users/documentation/case-studies/reduce_sum_tutorial.html).

- stanc_options:

  (list) Any Stan-to-C++ transpiler options to be used when compiling
  the model. See the **Examples** section below as well as the `stanc`
  chapter of the CmdStan Guide for more details on available options:
  https://mc-stan.org/docs/cmdstan-guide/stanc.html.

- force_recompile:

  (logical) Should the model be recompiled even if was not modified
  since last compiled. The default is `FALSE`. Can also be set via a
  global `cmdstanr_force_recompile` option.

- seed:

  (positive integer(s)) A seed for the (P)RNG to pass to CmdStan. In the
  case of multi-chain sampling the single `seed` will automatically be
  augmented by the the run (chain) ID so that each chain uses a
  different seed. The exception is the transformed data block, which
  defaults to using same seed for all chains so that the same data is
  generated for all chains if RNG functions are used. The only time
  `seed` should be specified as a vector (one element per chain) is if
  RNG functions are used in transformed data and the goal is to generate
  *different* data for each chain.

- output_dir:

  (string) A path to a directory where CmdStan should write its output
  CSV files. For MCMC there will be one file per chain; for other
  methods there will be a single file. For interactive use this can
  typically be left at `NULL` (temporary directory) since CmdStanR makes
  the CmdStan output (posterior draws and diagnostics) available in R
  via methods of the fitted model objects. This can be set for an entire
  R session using `options(cmdstanr_output_dir)`. The behavior of
  `output_dir` is as follows:

  - If `NULL` (the default), then the CSV files are written to a
    temporary directory and only saved permanently if the user calls one
    of the `$save_*` methods of the fitted model object (e.g.,
    [`$save_output_files()`](https://mc-stan.org/cmdstanr/reference/fit-method-save_output_files.html)).
    These temporary files are removed when the fitted model object is
    [garbage collected](https://rdrr.io/r/base/gc.html) (manually or
    automatically).

  - If a path, then the files are created in `output_dir` with names
    corresponding to the defaults used by `$save_output_files()`.

- sig_figs:

  (positive integer) The number of significant figures used when storing
  the output values. By default, CmdStan represent the output values
  with 6 significant figures. The upper limit for `sig_figs` is 18.
  Increasing this value will result in larger output CSV files and thus
  an increased usage of disk space.

- parallel_chains:

  (positive integer) The *maximum* number of MCMC chains to run in
  parallel. If `parallel_chains` is not specified then the default is to
  look for the option `"mc.cores"`, which can be set for an entire R
  session by `options(mc.cores=value)`. If the `"mc.cores"` option has
  not been set then the default is `1`.

- threads_per_chain:

  (positive integer) If the model was
  [compiled](https://mc-stan.org/cmdstanr/reference/model-method-compile.html)
  with threading support, the number of threads to use in parallelized
  sections *within* an MCMC chain (e.g., when using the Stan functions
  `reduce_sum()` or `map_rect()`). This is in contrast with
  `parallel_chains`, which specifies the number of chains to run in
  parallel. The actual number of CPU cores used is
  `parallel_chains*threads_per_chain`. For an example of using threading
  see the Stan case study [Reduce Sum: A Minimal
  Example](https://mc-stan.org/users/documentation/case-studies/reduce_sum_tutorial.html).

## Value

A `CmdStanFit` object.
