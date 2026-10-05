# Local Stan model compilation

`tar_stan_compile()` creates a target to compile a Stan model on the
local file system and return the original Stan model file. Does not
compile the model if the compilation is already up to date.

## Usage

``` r
tar_stan_compile(
  name,
  stan_file,
  quiet = TRUE,
  stdout = NULL,
  stderr = NULL,
  dir = NULL,
  pedantic = FALSE,
  include_paths = NULL,
  cpp_options = list(),
  stanc_options = list(),
  force_recompile = FALSE,
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

  Symbol, name of the target. In
  [`tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html),
  `name` is an unevaluated symbol, e.g. `tar_target(name = data)`. In
  [`tar_target_raw()`](https://docs.ropensci.org/targets/reference/tar_target.html),
  `name` is a character string, e.g. `tar_target_raw(name = "data")`.

  A target name must be a valid name for a symbol in R, and it must not
  start with a dot. Subsequent targets can refer to this name
  symbolically to induce a dependency relationship: e.g.
  `tar_target(downstream_target, f(upstream_target))` is a target named
  `downstream_target` which depends on a target `upstream_target` and a
  function `f()`.

  In most cases, The target name is the name of its local data file in
  storage. Some file systems are not case sensitive, which means
  converting a name to a different case may overwrite a different
  target. Please ensure all target names have unique names when
  converted to lower case.

  In addition, a target's name determines its random number generator
  seed. In this way, each target runs with a reproducible seed so
  someone else running the same pipeline should get the same results,
  and no two targets in the same pipeline share the same seed. (Even
  dynamic branches have different names and thus different seeds.) You
  can recover the seed of a completed target with
  `tar_meta(your_target, seed)` and run
  [`tar_seed_set()`](https://docs.ropensci.org/targets/reference/tar_seed_set.html)
  on the result to locally recreate the target's initial RNG state.

- stan_file:

  (string) The path to a `.stan` file containing a Stan program. The
  helper function
  [`write_stan_file()`](https://mc-stan.org/cmdstanr/reference/write_stan_file.html)
  is provided for cases when it is more convenient to specify the Stan
  program as a string. If `stan_file` is not specified then `exe_file`
  must be specified.

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

`tar_stan_compile()` returns a target object to compile a Stan file. The
return value of this target is a character vector containing the Stan
model source file and compiled executable file. A change in either file
will cause the target to rerun in the next run of the pipeline. See the
"Target objects" section for background.

## Details

Most of the arguments are passed to the `$compile()` method of the
`CmdStanModel` class. For details, visit
<https://mc-stan.org/cmdstanr/reference/>.

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
if (Sys.getenv("TAR_LONG_EXAMPLES") == "true") {
targets::tar_dir({ # tar_dir() runs code from a temporary directory.
targets::tar_script({
library(stantargets)
# Do not use temporary storage for stan files in real projects
# or else your targets will always rerun.
path <- tempfile(pattern = "", fileext = ".stan")
tar_stan_example_file(path = path)
list(tar_stan_compile(compiled_model, path))
})
targets::tar_make()
})
}
```
