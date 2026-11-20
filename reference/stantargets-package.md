# targets: Targets Archetypes for Stan

Bayesian data analysis usually incurs long runtimes and cumbersome
custom code. A pipeline toolkit tailored to Bayesian statisticians, the
`stantargets` R package leverages `targets` and `cmdstanr` to ease these
burdens. `stantargets` makes it super easy to set up scalable Stan
pipelines that automatically parallelize the computation and skip
expensive steps when the results are already up to date. Minimal custom
code is required, and there is no need to manually configure branching,
so usage is much easier than `targets` alone. `stantargets` can access
all of `cmdstanr`'s major algorithms (MCMC, variational Bayes, and
optimization) and it supports both single-fit workflows and multi-rep
simulation studies.

## See also

<https://docs.ropensci.org/stantargets/>,
[`tar_stan_mcmc()`](https://docs.ropensci.org/stantargets/reference/tar_stan_mcmc.md)
