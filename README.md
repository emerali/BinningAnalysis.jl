[![travis][travis-img]](https://travis-ci.org/crstnbr/BinningAnalysis.jl)
[![appveyor][appveyor-img]](https://ci.appveyor.com/project/crstnbr/binninganalysis-jl/branch/master)
[![codecov][codecov-img]](http://codecov.io/github/crstnbr/BinningAnalysis.jl?branch=master)
<!-- [![coveralls][coveralls-img]](https://coveralls.io/github/crstnbr/BinningAnalysis.jl?branch=master) !-->
[![license: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

[travis-img]: https://img.shields.io/travis/crstnbr/BinningAnalysis.jl/master.svg?label=linux
[appveyor-img]: https://img.shields.io/appveyor/ci/crstnbr/binninganalysis-jl/master.svg?label=windows
[codecov-img]: https://img.shields.io/codecov/c/github/crstnbr/BinningAnalysis.jl/master.svg?label=codecov
[coveralls-img]: https://img.shields.io/coveralls/github/crstnbr/BinningAnalysis.jl/master.svg?label=coverage

![logo](https://github.com/crstnbr/BinningAnalysis.jl/blob/master/docs/src/assets/logo_with_text.png)

This package implements the following binning tools,

* Logarithmic Binning (most performant)
  * Size complexity: $\mathcal{O}(\log_2(N))$
  * Time complexity: $\mathcal{O}(N)$
* Full Binning

and the following statistical resampling methods,

* Jackknife resampling.

As per usual, you can install the package with

```julia
] add https://github.com/crstnbr/BinningAnalysis.jl
```

---

### Hands on: Logarithmic Binning

```julia
B = LogBinner()
# As per default, 2^32-1 ≈ 4 billion values can be added to the binner. This value can be
# tuned with the `capacity` keyword argument.

push!(B, 4.2)
append!(B, [1,2,3]) # multiple values at once

x  = mean(B)
Δx = std_error(B) # standard error of the mean
tau_x = tau(B) # autocorrelation time
```

<!--
# You can also get the standard error estimates for all binning levels individually.
Δxs = all_std_errors(B)

# BETA: Check whether a level has converged
has_converged(B, 3)
# This checks whether variance/N of level 2 and 3 is approximately the same.
# To be sure that the binning analysis has converged, this criterion should be
# true over multiple levels.
# Note that this criterion is generally not true close to the maximum binning
# level. Usually this is the result of the small effective sample size, rather
# than a convergence failure.
!-->

### Hands on: Full Binning

```julia
x = rand(1000)
B = FullBinner(x) # just a thin wrapper <: AbstractVector

push!(B, 2.0) # will modify x
append!(B, [1,2,3])

x  = mean(B)
Δx = std_error(B) # standard error of the mean
```

### Hands on: Jackknife

```julia
x = rand(100)

Δx = jackknife(mean, x) # jackknife error of <x>

isapprox(jackknife(mean, ts), std(ts)/sqrt(length(ts))) == true

Δx_inv = jackknife(x -> mean(1 ./ x), x) # jacknife error of <1/x>

# Multiple time series
x = rand(100)
y = rand(100)

# The input z will be a matrix whose columns correspond to x and y, i.e. z[:,1] == x and z[:,2] == y
g(z) = @views mean(z[:,1]) * mean(z[:,2]) / mean(z[:,1] .* z[:,2])  # <x><y> / <xy>
Δg = jackknife(g, x, y)
```
