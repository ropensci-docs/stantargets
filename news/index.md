# Changelog

## stantargets 0.1.3

- Switch from `qs` to `qs2`.
- Align with `cmdstanr` 0.9.0 argument signatures (especially
  variational Bayes).

## stantargets 0.1.2

- Add R-multiverse topic.

## stantargets 0.1.1

### Invalidating changes

- To align with <https://github.com/ropensci/targets/issues/1244> and
  <https://github.com/ropensci/targets/pull/1262>, switch the hashing
  functions from
  [`digest::digest()`](https://eddelbuettel.github.io/digest/man/digest.html)
  to
  [`secretbase::siphash13()`](https://shikokuchuo.net/secretbase/reference/siphash13.html).

### Other changes

- Remove temporary files generated CmdStan.
- Add the new `description` arguments of
  [`tar_target()`](https://docs.ropensci.org/targets/reference/tar_target.html)
  (\`targets \>= 1.5.1.9001).
- Append model file information to the target descriptions using
  `tar_map()` (`tarchetypes` \>= 0.7.12.9001).

## stantargets 0.1.0

- Add a new `transform` argument to the `tar_stan_*_rep_draws()`
  functions (except `mle`) to support simulation-based calibration (SBC)
  ([\#31](https://github.com/ropensci/stantargets/issues/31),
  [@mike-lawrence](https://github.com/mike-lawrence),
  [@martinmodrak](https://github.com/martinmodrak),
  [@dmi3kno](https://github.com/dmi3kno)).

## stantargets 0.0.6

- Implement resilient rep-specific seeds that do not change if the
  batching structure changes.

## stantargets 0.0.5

- Update the signatures of most user-side functions to accommodate
  changes from `cmdstanr` 0.2.0 to 0.5.0
  (<https://github.com/ropensci/targets/discussions/884>,
  [@stuvet](https://github.com/stuvet)).
- Add new arguments `variables_fit` and `inc_warmup_fit` to be passed to
  the `CmdStanFit` target in
  [`tar_stan_mcmc()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc.md)
  instead of `variables` and `inc_warmup`
  ([\#56](https://github.com/ropensci/stantargets/issues/56),
  [@yizhang-yiz](https://github.com/yizhang-yiz)). Same for the
  `variables` argument of
  [`tar_stan_gq()`](https://docs.ropensci.org/stantargets/reference/tar_stan_gq.md),
  [`tar_stan_mle()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mle.md),
  and
  [`tar_stan_vb()`](https://docs.ropensci.org/stantargets/reference/tar_stan_vb.md).

## stantargets 0.0.4

- Support the `repository` argument for `targets` \>= 0.11.0.
- Append a new `.dataset_id` column to target outputs to aid in model
  comparisons across the same datasets.

## stantargets 0.0.3

- Update docs to changes in `cmdstanr`, `posterior`, and `targets`.

## stantargets 0.0.2

- Reference JOSS paper.

## stantargets 0.0.1

- Skip tests if CmdStan is not installed
  ([@sakrejda](https://github.com/sakrejda)).
- Use custom `generate_data()` function in the docs, as opposed to
  [`tar_stan_example_data()`](https://docs.ropensci.org/stantargets/reference/tar_stan_example_data.md)
  directly ([@sakrejda](https://github.com/sakrejda)).
- Add the `pedantic` argument for compilation
  ([@sakrejda](https://github.com/sakrejda)).
- Reduce dependencies on some `rlang` functions like
  [`sym()`](https://rlang.r-lib.org/reference/sym.html)
  ([@sakrejda](https://github.com/sakrejda)).
- Change `trn()` to
  [`if_any()`](https://dplyr.tidyverse.org/reference/across.html)
  ([@mattwarkentin](https://github.com/mattwarkentin),
  [@sakrejda](https://github.com/sakrejda),
  [@tjmahr](https://github.com/tjmahr)).
- Add [@sakrejda](https://github.com/sakrejda) and
  [@mattwarkentin](https://github.com/mattwarkentin) as reviewers in the
  `DESCRIPTION`.
- Talk about the R package and system dependencies of `stantargets` in
  the README ([@mattwarkentin](https://github.com/mattwarkentin)).
- Throw an error earlier if the Stan file does not exist
  ([@sakrejda](https://github.com/sakrejda),
  [@mattwarkentin](https://github.com/mattwarkentin))
- Use `@format` `roxygen2` tag for data generation
  ([@mattwarkentin](https://github.com/mattwarkentin)).
- Use `@family` go cross-reference functions
  ([@mattwarkentin](https://github.com/mattwarkentin)).
- Elaborate on the roles and return values of specific targets generated
  by target factories
  ([@mattwarkentin](https://github.com/mattwarkentin)).
- Undergo rOpenSci peer review and transition to rOpenSci.
- Link to an example project.

## stantargets 0.0.0.9002

- Return the executable file after the Stan source file in model
  compilation targets.
- Replace the `log` argument with `stdout` and `stderr`
  ([\#23](https://github.com/ropensci/stantargets/issues/23)).
- Switch meaning of `%||%` and `%|||%` to conform to historical
  precedent.

## stantargets 0.0.0.9001

- Join on data to summary output using `.join_data` in the Stan data
  ([\#18](https://github.com/ropensci/stantargets/issues/18)).
- Pre-compile models for testing and add an environment variable to skip
  tests that always force recompilation
  ([\#19](https://github.com/ropensci/stantargets/issues/19)).
- Load packages for any target computing summaries.

## stantargets 0.0.0.9000

- Added a `NEWS.md` file to track changes to the package.
