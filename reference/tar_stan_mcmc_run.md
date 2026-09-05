# Compile and run a Stan model and return the `CmdStanFit` object.

Not a user-side function. Do not invoke directly.

## Usage

``` r
tar_stan_mcmc_run(
  stan_file,
  data,
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
  refresh,
  init,
  save_latent_dynamics,
  output_dir,
  output_basename,
  sig_figs,
  chains,
  parallel_chains,
  chain_ids,
  threads_per_chain,
  opencl_ids,
  iter_warmup,
  iter_sampling,
  save_warmup,
  thin,
  max_treedepth,
  adapt_engaged,
  adapt_delta,
  step_size,
  metric,
  metric_file,
  inv_metric,
  init_buffer,
  term_buffer,
  window,
  fixed_param,
  show_messages,
  diagnostics,
  variables,
  inc_warmup
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

- compile:

  Character of length 1. If `"original"`, then `cmdstan` will compile
  the source file right before running it (or skip compilation if the
  binary is up to date). This assumes the worker has access to the file.
  If the worker is running on a remote computer that does not have
  access to the model file, set to `"copy"` instead. `compile = "copy"`
  means the pipeline will read the lines of the original Stan model file
  and send them to the worker. The worker writes the lines to a local
  copy and compiles the model from there, so it no longer needs access
  to the original Stan model file on your local machine. However, as a
  result, the Stan model re-compiles every time the main target reruns.

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

- refresh:

  (non-negative integer) The number of iterations between printed screen
  updates. If `refresh = 0`, only error messages will be printed.

- init:

  (multiple options) The initialization method to use for the variables
  declared in the parameters block of the Stan program. One of the
  following:

  - A real number `x>0`. This initializes *all* parameters randomly
    between `[-x,x]` on the *unconstrained* parameter space.;

  - The number `0`. This initializes *all* parameters to `0`;

  - A character vector of paths (one per chain) to JSON or Rdump files
    containing initial values for all or some parameters. See
    [`write_stan_json()`](https://mc-stan.org/cmdstanr/reference/write_stan_json.html)
    to write R objects to JSON files compatible with CmdStan.

  - A list of lists containing initial values for all or some
    parameters. For MCMC the list should contain a sublist for each
    chain. For other model fitting methods there should be just one
    sublist. The sublists should have named elements corresponding to
    the parameters for which you are specifying initial values. See
    **Examples**.

  - A function that returns a single list with names corresponding to
    the parameters for which you are specifying initial values. The
    function can take no arguments or a single argument `chain_id`. For
    MCMC, if the function has argument `chain_id` it will be supplied
    with the chain id (from 1 to number of chains) when called to
    generate the initial values. See **Examples**.

  - A
    [`CmdStanMCMC`](https://mc-stan.org/cmdstanr/reference/CmdStanMCMC.html),
    [`CmdStanMLE`](https://mc-stan.org/cmdstanr/reference/CmdStanMLE.html),
    [`CmdStanVB`](https://mc-stan.org/cmdstanr/reference/CmdStanVB.html),
    [`CmdStanPathfinder`](https://mc-stan.org/cmdstanr/reference/CmdStanPathfinder.html),
    or
    [`CmdStanLaplace`](https://mc-stan.org/cmdstanr/reference/CmdStanLaplace.html)
    fit object. If the fit object's parameters are only a subset of the
    model parameters then the other parameters will be drawn by Stan's
    default initialization. The fit object must have at least some
    parameters that are the same name and dimensions as the current Stan
    model. For the `sample` and `pathfinder` method, if the fit object
    has fewer draws than the requested number of chains/paths then the
    inits will be drawn using sampling with replacement. Otherwise
    sampling without replacement will be used. When a
    [`CmdStanPathfinder`](https://mc-stan.org/cmdstanr/reference/CmdStanPathfinder.html)
    fit object is used as the init, if . `psis_resample` was set to
    `FALSE` and `calculate_lp` was set to `TRUE` (default), then
    resampling without replacement with Pareto smoothed weights will be
    used. If `psis_resample` was set to `TRUE` or `calculate_lp` was set
    to `FALSE` then sampling without replacement with uniform weights
    will be used to select the draws. PSIS resampling is used to select
    the draws for
    [`CmdStanVB`](https://mc-stan.org/cmdstanr/reference/CmdStanVB.html),
    and
    [`CmdStanLaplace`](https://mc-stan.org/cmdstanr/reference/CmdStanLaplace.html)
    fit objects.

  - A type inheriting from
    [`posterior::draws`](https://mc-stan.org/posterior/reference/draws.html).
    If the draws object has less samples than the number of requested
    chains/paths then the inits will be drawn using sampling with
    replacement. Otherwise sampling without replacement will be used. If
    the draws object's parameters are only a subset of the model
    parameters then the other parameters will be drawn by Stan's default
    initialization. The fit object must have at least some parameters
    that are the same name and dimensions as the current Stan model.

- save_latent_dynamics:

  (logical) Should auxiliary diagnostic information about the latent
  dynamics be written to temporary diagnostic CSV files? This argument
  replaces CmdStan's `diagnostic_file` argument and the content written
  to CSV is controlled by the user's CmdStan installation and not
  CmdStanR (for some algorithms no content may be written). The default
  is `FALSE`, which is appropriate for almost every use case. To save
  the temporary files created when `save_latent_dynamics=TRUE` see the
  [`$save_latent_dynamics_files()`](https://mc-stan.org/cmdstanr/reference/fit-method-save_output_files.html)
  method.

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

- output_basename:

  (string) A string to use as a prefix for the names of the output CSV
  files of CmdStan. If `NULL` (the default), the basename of the output
  CSV files will be comprised from the model name, timestamp, and 5
  random characters.

- sig_figs:

  (positive integer) The number of significant figures used when storing
  the output values. By default, CmdStan represent the output values
  with 6 significant figures. The upper limit for `sig_figs` is 18.
  Increasing this value will result in larger output CSV files and thus
  an increased usage of disk space.

- chains:

  (positive integer) The number of Markov chains to run. The default is
  4.

- parallel_chains:

  (positive integer) The *maximum* number of MCMC chains to run in
  parallel. If `parallel_chains` is not specified then the default is to
  look for the option `"mc.cores"`, which can be set for an entire R
  session by `options(mc.cores=value)`. If the `"mc.cores"` option has
  not been set then the default is `1`.

- chain_ids:

  (integer vector) A vector of chain IDs. Must contain as many unique
  positive integers as the number of chains. If not set, the default
  chain IDs are used (integers starting from `1`).

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

- opencl_ids:

  (integer vector of length 2) The platform and device IDs of the OpenCL
  device to use for fitting. The model must be compiled with
  `cpp_options = list(stan_opencl = TRUE)` for this argument to have an
  effect.

- iter_warmup:

  (positive integer) The number of warmup iterations to run per chain.
  Note: in the CmdStan User's Guide this is referred to as `num_warmup`.

- iter_sampling:

  (positive integer) The number of post-warmup iterations to run per
  chain. Note: in the CmdStan User's Guide this is referred to as
  `num_samples`.

- save_warmup:

  (logical) Should warmup iterations be saved? The default is `FALSE`.

- thin:

  (positive integer) The period between saved samples. This should
  typically be left at its default (no thinning) unless memory is a
  problem.

- max_treedepth:

  (positive integer) The maximum allowed tree depth for the NUTS engine.
  See the *Tree Depth* section of the CmdStan User's Guide for more
  details.

- adapt_engaged:

  (logical) Do warmup adaptation? The default is `TRUE`. If a
  precomputed inverse metric is specified via the `inv_metric` argument
  (or `metric_file`) then, if `adapt_engaged=TRUE`, Stan will use the
  provided inverse metric just as an initial guess during adaptation. To
  turn off adaptation when using a precomputed inverse metric set
  `adapt_engaged=FALSE`.

- adapt_delta:

  (real in `(0,1)`) The adaptation target acceptance statistic.

- step_size:

  (positive real) The *initial* step size for the discrete approximation
  to continuous Hamiltonian dynamics. This is further tuned during
  warmup.

- metric:

  (string) One of `"diag_e"`, `"dense_e"`, or `"unit_e"`, specifying the
  geometry of the base manifold. See the *Euclidean Metric* section of
  the CmdStan User's Guide for more details. To specify a precomputed
  (inverse) metric, see the `inv_metric` argument below.

- metric_file:

  (character vector) The paths to JSON or Rdump files (one per chain)
  compatible with CmdStan that contain precomputed inverse metrics. The
  `metric_file` argument is inherited from CmdStan but is confusing in
  that the entry in JSON or Rdump file(s) must be named `inv_metric`,
  referring to the *inverse* metric. We recommend instead using
  CmdStanR's `inv_metric` argument (see below) to specify an inverse
  metric directly using a vector or matrix from your R session.

- inv_metric:

  (vector, matrix) A vector (if `metric='diag_e'`) or a matrix (if
  `metric='dense_e'`) for initializing the inverse metric. This can be
  used as an alternative to the `metric_file` argument. A vector is
  interpreted as a diagonal metric. The inverse metric is usually set to
  an estimate of the posterior covariance. See the `adapt_engaged`
  argument above for details about (and control over) how specifying a
  precomputed inverse metric interacts with adaptation.

- init_buffer:

  (nonnegative integer) Width of initial fast timestep adaptation
  interval during warmup.

- term_buffer:

  (nonnegative integer) Width of final fast timestep adaptation interval
  during warmup.

- window:

  (nonnegative integer) Initial width of slow timestep/metric adaptation
  interval.

- fixed_param:

  (logical) When `TRUE`, call CmdStan with argument
  `"algorithm=fixed_param"`. The default is `FALSE`. The fixed parameter
  sampler generates a new sample without changing the current state of
  the Markov chain; only generated quantities may change. This can be
  useful when, for example, trying to generate pseudo-data using the
  generated quantities block. If the parameters block is empty then
  using `fixed_param=TRUE` is mandatory. When `fixed_param=TRUE` the
  `chains` and `parallel_chains` arguments will be set to `1`.

- show_messages:

  (logical) When `TRUE` (the default), prints all output during the
  execution process, such as iteration numbers and elapsed times. If the
  output is silenced then the
  [`$output()`](https://mc-stan.org/cmdstanr/reference/fit-method-output.html)
  method of the resulting fit object can be used to display the silenced
  messages.

- diagnostics:

  (character vector) The diagnostics to automatically check and warn
  about after sampling. Setting this to an empty string `""` or `NULL`
  can be used to prevent CmdStanR from automatically reading in the
  sampler diagnostics from CSV if you wish to manually read in the
  results and validate them yourself, for example using
  [`read_cmdstan_csv()`](https://mc-stan.org/cmdstanr/reference/read_cmdstan_csv.html).
  The currently available diagnostics are `"divergences"`,
  `"treedepth"`, and `"ebfmi"` (the default is to check all of them).

  These diagnostics are also available after fitting. The
  [`$sampler_diagnostics()`](https://mc-stan.org/cmdstanr/reference/fit-method-sampler_diagnostics.html)
  method provides access the diagnostic values for each iteration and
  the
  [`$diagnostic_summary()`](https://mc-stan.org/cmdstanr/reference/fit-method-diagnostic_summary.html)
  method provides summaries of the diagnostics and can regenerate the
  warning messages.

  Diagnostics like R-hat and effective sample size are *not* currently
  available via the `diagnostics` argument but can be checked after
  fitting using the
  [`$summary()`](https://mc-stan.org/cmdstanr/reference/fit-method-summary.html)
  method.

## Value

A `CmdStanFit` object.
