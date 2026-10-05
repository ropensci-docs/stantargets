# Multiple MCMCs per model with sampler diagnostics

`tar_stan_mcmc_rep_diagnostics()` creates targets to run MCMC multiple
times per model and save only the sampler diagnostics from each run.

## Usage

``` r
tar_stan_mcmc_rep_diagnostics(
  name,
  stan_files,
  data = list(),
  batches = 1L,
  reps = 1L,
  combine = FALSE,
  compile = c("original", "copy"),
  quiet = TRUE,
  stdout = NULL,
  stderr = NULL,
  dir = NULL,
  pedantic = FALSE,
  include_paths = NULL,
  cpp_options = list(),
  stanc_options = list(),
  force_recompile = FALSE,
  seed = NULL,
  refresh = NULL,
  init = NULL,
  save_latent_dynamics = FALSE,
  output_dir = NULL,
  output_basename = NULL,
  sig_figs = NULL,
  chains = 4,
  parallel_chains = getOption("mc.cores", 1),
  chain_ids = seq_len(chains),
  threads_per_chain = NULL,
  opencl_ids = NULL,
  iter_warmup = NULL,
  iter_sampling = NULL,
  save_warmup = FALSE,
  thin = NULL,
  max_treedepth = NULL,
  adapt_engaged = TRUE,
  adapt_delta = NULL,
  step_size = NULL,
  metric = NULL,
  metric_file = NULL,
  inv_metric = NULL,
  init_buffer = NULL,
  term_buffer = NULL,
  window = NULL,
  fixed_param = FALSE,
  show_messages = TRUE,
  diagnostics = c("divergences", "treedepth", "ebfmi"),
  inc_warmup = FALSE,
  data_copy = character(0),
  tidy_eval = targets::tar_option_get("tidy_eval"),
  packages = targets::tar_option_get("packages"),
  library = targets::tar_option_get("library"),
  format = "qs",
  format_df = "fst_tbl",
  repository = targets::tar_option_get("repository"),
  error = targets::tar_option_get("error"),
  memory = "transient",
  garbage_collection = TRUE,
  deployment = targets::tar_option_get("deployment"),
  priority = targets::tar_option_get("priority"),
  resources = targets::tar_option_get("resources"),
  storage = targets::tar_option_get("storage"),
  retrieval = targets::tar_option_get("retrieval"),
  cue = targets::tar_option_get("cue"),
  description = targets::tar_option_get("description")
)
```

## Arguments

- name:

  Symbol, base name for the collection of targets. Serves as a prefix
  for target names.

- stan_files:

  Character vector of paths to known existing Stan model files created
  before running the pipeline.

- data:

  Code to generate a single replication of a simulated dataset. The
  workflow simulates multiple datasets, and each model runs on each
  dataset. To join data on to the model summaries, include a
  `.join_data` element of your Stan data list with names and dimensions
  corresponding to those of the model. For details, read
  <https://docs.ropensci.org/stantargets/articles/simulation.html>.

- batches:

  Number of batches. Each batch is a sequence of branch targets
  containing multiple reps. Each rep generates a dataset and runs the
  model on it.

- reps:

  Number of replications per batch.

- combine:

  Logical, whether to create a target to combine all the model results
  into a single data frame downstream. Convenient, but duplicates data.

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

- inc_warmup:

  (logical) Should warmup draws be included? Defaults to `FALSE`.
  Ignored except when used with
  [CmdStanMCMC](https://mc-stan.org/cmdstanr/reference/CmdStanMCMC.html)
  objects.

- data_copy:

  Character vector of names of scalars in `data`. These values will be
  inserted as columns in the output data frame for each rep. To join
  more than just scalars, include a `.join_data` element of your Stan
  data list with names and dimensions corresponding to those of the
  model. For details, read
  <https://docs.ropensci.org/stantargets/articles/simulation.html>.

- tidy_eval:

  Logical, whether to enable tidy evaluation when interpreting `command`
  and `pattern`. If `TRUE`, you can use the "bang-bang" operator `!!` to
  programmatically insert the values of global objects.

- packages:

  Character vector of packages to load right before the target runs or
  the output data is reloaded for downstream targets. Use
  [`tar_option_set()`](https://docs.ropensci.org/targets/reference/tar_option_set.html)
  to set packages globally for all subsequent targets you define.

- library:

  Character vector of library paths to try when loading `packages`.

- format:

  Character of length 1, storage format of the data frame of posterior
  summaries. We recommend efficient data frame formats such as
  `"feather"` or `"aws_parquet"`. For more on storage formats, see the
  help file of
  [`targets::tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html).

- format_df:

  Character of length 1, storage format of the data frame targets such
  as posterior draws. We recommend efficient data frame formats such as
  `"feather"` or `"aws_parquet"`. For more on storage formats, see the
  help file of
  [`targets::tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html).

- repository:

  Character of length 1, remote repository for target storage. Choices:

  - `"local"`: file system of the local machine.

  - `"aws"`: Amazon Web Services (AWS) S3 bucket. Can be configured with
    a non-AWS S3 bucket using the `endpoint` argument of
    [`tar_resources_aws()`](https://docs.ropensci.org/targets/reference/tar_resources_aws.html),
    but versioning capabilities may be lost in doing so. See the cloud
    storage section of <https://books.ropensci.org/targets/data.html>
    for details for instructions.

  - `"gcp"`: Google Cloud Platform storage bucket. See the cloud storage
    section of <https://books.ropensci.org/targets/data.html> for
    details for instructions.

  - A character string from
    [`tar_repository_cas()`](https://docs.ropensci.org/targets/reference/tar_repository_cas.html)
    for content-addressable storage.

  Note: if `repository` is not `"local"` and `format` is `"file"` then
  the target should create a single output file. That output file is
  uploaded to the cloud and tracked for changes where it exists in the
  cloud. As of `targets` version 1.11.0 and higher, the local file is no
  longer deleted after the target runs.

- error:

  Character of length 1, what to do if the target stops and throws an
  error. Options:

  - `"stop"`: the whole pipeline stops and throws an error.

  - `"continue"`: the whole pipeline keeps going.

  - `"null"`: The errored target continues and returns `NULL`. The data
    hash is deliberately wrong so the target is not up to date for the
    next run of the pipeline. In addition, as of `targets` version
    1.8.0.9011, a value of `NULL` is given to upstream dependencies with
    `error = "null"` if loading fails.

  - `"abridge"`: any currently running targets keep running, but no new
    targets launch after that.

  - `"trim"`: all currently running targets stay running. A queued
    target is allowed to start if:

    1.  It is not downstream of the error, and

    2.  It is not a sibling branch from the same
        [`tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html)
        call (if the error happened in a dynamic branch).

    The idea is to avoid starting any new work that the immediate error
    impacts. `error = "trim"` is just like `error = "abridge"`, but it
    allows potentially healthy regions of the dependency graph to begin
    running. (Visit <https://books.ropensci.org/targets/debugging.html>
    to learn how to debug targets using saved workspaces.)

- memory:

  Character of length 1, memory strategy. Possible values:

  - `"auto"` (default): equivalent to `memory = "transient"` in almost
    all cases. But to avoid superfluous reads from disk,
    `memory = "auto"` is equivalent to `memory = "persistent"` for for
    non-dynamically-branched targets that other targets dynamically
    branch over. For example: if your pipeline has
    `tar_target(name = y, command = x, pattern = map(x))`, then
    `tar_target(name = x, command = f(), memory = "auto")` will use
    persistent memory for `x` in order to avoid rereading all of `x` for
    every branch of `y`.

  - `"transient"`: the target gets unloaded after every new target
    completes. Either way, the target gets automatically loaded into
    memory whenever another target needs the value.

  - `"persistent"`: the target stays in memory until the end of the
    pipeline (unless `storage` is `"worker"`, in which case `targets`
    unloads the value from memory right after storing it in order to
    avoid sending copious data over a network).

  For cloud-based file targets (e.g. `format = "file"` with
  `repository = "aws"`), the `memory` option applies to the temporary
  local copy of the file: `"persistent"` means it remains until the end
  of the pipeline and is then deleted, and `"transient"` means it gets
  deleted as soon as possible. The former conserves bandwidth, and the
  latter conserves local storage.

- garbage_collection:

  Logical: `TRUE` to run [`base::gc()`](https://rdrr.io/r/base/gc.html)
  just before the target runs, in whatever R process it is about to run
  (which could be a parallel worker). `FALSE` to omit garbage
  collection. Numeric values get converted to `FALSE`. The
  `garbage_collection` option in
  [`tar_option_set()`](https://docs.ropensci.org/targets/reference/tar_option_set.html)
  is independent of the argument of the same name in
  [`tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html).

- deployment:

  Character of length 1. If `deployment` is `"main"`, then the target
  will run on the central controlling R process. Otherwise, if
  `deployment` is `"worker"` and you set up the pipeline with
  distributed/parallel computing, then the target runs on a parallel
  worker. For more on distributed/parallel computing in `targets`,
  please visit <https://books.ropensci.org/targets/crew.html>.

- priority:

  Deprecated on 2025-04-08 (`targets` version 1.10.1.9013). `targets`
  has moved to a more efficient scheduling algorithm
  (<https://github.com/ropensci/targets/issues/1458>) which cannot
  support priorities. The `priority` argument of
  [`tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html)
  no longer has a reliable effect on execution order.

- resources:

  Object returned by
  [`tar_resources()`](https://docs.ropensci.org/targets/reference/tar_resources.html)
  with optional settings for high-performance computing functionality,
  alternative data storage formats, and other optional capabilities of
  `targets`. See
  [`tar_resources()`](https://docs.ropensci.org/targets/reference/tar_resources.html)
  for details.

- storage:

  Character string to control when the output of the target is saved to
  storage. Only relevant when using `targets` with parallel workers
  (<https://books.ropensci.org/targets/crew.html>). Must be one of the
  following values:

  - `"worker"` (default): the worker saves/uploads the value.

  - `"main"`: the target's return value is sent back to the host machine
    and saved/uploaded locally.

  - `"none"`: `targets` makes no attempt to save the result of the
    target to storage in the location where `targets` expects it to be.
    Saving to storage is the responsibility of the user. Use with
    caution.

- retrieval:

  Character string to control when the current target loads its
  dependencies into memory before running. (Here, a "dependency" is
  another target upstream that the current one depends on.) Only
  relevant when using `targets` with parallel workers
  (<https://books.ropensci.org/targets/crew.html>). Must be one of the
  following values:

  - `"auto"` (default): equivalent to `retrieval = "worker"` in almost
    all cases. But to avoid unnecessary reads from disk,
    `retrieval = "auto"` is equivalent to `retrieval = "main"` for
    dynamic branches that branch over non-dynamic targets. For example:
    if your pipeline has `tar_target(x, command = f())`, then
    `tar_target(y, command = x, pattern = map(x), retrieval = "auto")`
    will use `"main"` retrieval in order to avoid rereading all of `x`
    for every branch of `y`.

  - `"worker"`: the worker loads the target's dependencies.

  - `"main"`: the target's dependencies are loaded on the host machine
    and sent to the worker before the target runs.

  - `"none"`: `targets` makes no attempt to load its dependencies. With
    `retrieval = "none"`, loading dependencies is the responsibility of
    the user. Use with caution.

- cue:

  An optional object from
  [`tar_cue()`](https://docs.ropensci.org/targets/reference/tar_cue.html)
  to customize the rules that decide whether the target is up to date.

- description:

  Character of length 1, a custom free-form human-readable text
  description of the target. Descriptions appear as target labels in
  functions like
  [`tar_manifest()`](https://docs.ropensci.org/targets/reference/tar_manifest.html)
  and
  [`tar_visnetwork()`](https://docs.ropensci.org/targets/reference/tar_visnetwork.html),
  and they let you select subsets of targets for the `names` argument of
  functions like
  [`tar_make()`](https://docs.ropensci.org/targets/reference/tar_make.html).
  For example,
  `tar_manifest(names = tar_described_as(starts_with("survival model")))`
  lists all the targets whose descriptions start with the character
  string `"survival model"`.

## Value

`tar_stan_mcmc_rep_diagnostics()` returns a list of target objects. See
the "Target objects" section for background. The target names use the
`name` argument as a prefix, and the individual elements of `stan_files`
appear in the suffixes where applicable. As an example, the specific
target objects returned by
`tar_stan_mcmc_rep_diagnostics(name = x, stan_files = "y.stan")` are as
follows.

- `x_file_y`: reproducibly track the Stan model file. Returns a
  character vector with the paths to the model file and compiled
  executable.

- `x_lines_y`: read the Stan model file for safe transport to parallel
  workers. Omitted if `compile = "original"`. Returns a character vector
  of lines in the model file.

- `x_data`: use dynamic branching to generate multiple datasets by
  repeatedly running the R expression in the `data` argument. Each
  dynamic branch returns a batch of Stan data lists that `x_y` supplies
  to the model.

- `x_y`: dynamic branching target to run MCMC once per dataset. Each
  dynamic branch returns a tidy data frames of HMC diagnostics
  corresponding to a batch of Stan data from `x_data`.

- `x`: combine all branches of `x_y` into a single non-dynamic target.
  Suppressed if `combine` is `FALSE`. Returns a long tidy data frame of
  HMC diagnostics.

## Details

Saved diagnostics could get quite large in storage, so please use
thinning if necessary.

Most of the arguments are passed to the `$compile()` and
`$generate_quantities()` methods of the `CmdStanModel` class. If you
previously compiled the model in an upstream
[`tar_stan_compile()`](https://docs.ropensci.org/stantargets/reference/tar_stan_compile.md)
target, then the model should not recompile.

## Seeds

Rep-specific random number generator seeds for the data and models are
automatically set based on the `seed` argument, batch, rep, parent
target name, and `tar_option_get("seed")`. This ensures the rep-specific
seeds do not change when you change the batching configuration (e.g. 40
batches of 10 reps each vs 20 batches of 20 reps each). Each data seed
is in the `.seed` list element of the output, and each Stan seed is in
the .seed column of each Stan model output.

## Target objects

Most `stantargets` functions are target factories, which means they
return target objects or lists of target objects. Target objects
represent skippable steps of the analysis pipeline as described at
<https://books.ropensci.org/targets/>. Please read the walkthrough at
<https://books.ropensci.org/targets/walkthrough.html> to understand the
role of target objects in analysis pipelines.

For developers,
<https://wlandau.github.io/targetopia/contributing.html#target-factories>
explains target factories (functions like this one which generate
targets) and the design specification at
<https://books.ropensci.org/targets-design/> details the structure and
composition of target objects.

## See also

Other MCMC:
[`tar_stan_mcmc()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc.md),
[`tar_stan_mcmc_rep_draws()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc_rep_draws.md),
[`tar_stan_mcmc_rep_summary()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc_rep_summary.md)

## Examples

``` r
if (Sys.getenv("TAR_LONG_EXAMPLES") == "true") {
targets::tar_dir({ # tar_dir() runs code from a temporary directory.
targets::tar_script({
library(stantargets)
# Do not use temporary storage for stan files in real projects
# or else your targets will always rerun.
path <- tempfile(pattern = "", fileext = ".stan")
tar_stan_example_file(path = path)
list(
  tar_stan_mcmc_rep_diagnostics(
    your_model,
    stan_files = path,
    data = tar_stan_example_data(),
    batches = 2,
    reps = 2,
    stdout = R.utils::nullfile(),
    stderr = R.utils::nullfile()
  )
)
}, ask = FALSE)
targets::tar_make()
})
}
```
