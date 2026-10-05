# One summary of a `CmdStanFit` object

Create a target to run the `$summary()` method of a `CmdStanFit` object.

## Usage

``` r
tar_stan_summary(
  name,
  fit,
  data = NULL,
  variables = NULL,
  summaries = NULL,
  summary_args = NULL,
  format = "fst_tbl",
  repository = targets::tar_option_get("repository"),
  error = targets::tar_option_get("error"),
  memory = targets::tar_option_get("memory"),
  garbage_collection = targets::tar_option_get("garbage_collection"),
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

- fit:

  Symbol, name of a `CmdStanFit` object or an upstream target that
  returns a `CmdStanFit` object.

- data:

  Code to generate the `data` for the Stan model.

- variables:

  (character vector) The variables to include.

- summaries:

  Optional list of summary functions passed to `...` in
  [`posterior::summarize_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html)
  through `$summary()` on the `CmdStanFit` object.

- summary_args:

  Optional list of summary function arguments passed to `.args` in
  [`posterior::summarize_draws()`](https://mc-stan.org/posterior/reference/draws_summary.html)
  through `$summary()` on the `CmdStanFit` object.

- format:

  Character of length 1, storage format of the data frame of posterior
  summaries. We recommend efficient data frame formats such as
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

`tar_stan_summary()` returns target object to summarize a `CmdStanFit`
object. The return value of the target is a tidy data frame of summaries
returned by the `$summary()` method of the `CmdStanFit` object. See the
"Target objects" section for background.

## Details

[`tar_stan_mcmc()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc.md)
etc. with `summary = TRUE` already gives you a target with output from
the `$summary()` method. Use `tar_stan_summary()` to create additional
specialized summaries.

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

## Examples

``` r
# First, write your Stan model file, e.g. model.stan.
# Then in _targets.R, write a pipeline like this:
if (Sys.getenv("TAR_LONG_EXAMPLES") == "true") {
targets::tar_dir({ # tar_dir() runs code from a temporary directory.
# Running inside a temporary directory to avoid
# modifying the user's file space. The file "model.stan"
# created below lives in a temporary directory.
# This satisfies CRAN policies.
tar_stan_example_file("model.stan")
targets::tar_script({
library(stantargets)
list(
  # Run a model and produce default summaries.
  tar_stan_mcmc(
    your_model,
    stan_files = "model.stan",
    data = tar_stan_example_data()
  ),
  # Produce a more specialized summary
  tar_stan_summary(
    your_summary,
    fit = your_model_mcmc_model,
    data = your_model_data_model,
    variables = "beta",
    summaries = list(~quantile(.x, probs = c(0.25, 0.75)))
  )
)}, ask = FALSE)
targets::tar_make()
})
}
```
