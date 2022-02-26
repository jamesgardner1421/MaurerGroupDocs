# Distributed Julia on Slurm clusters

Within Julia, there are [many options for parallel programming](https://docs.julialang.org/en/v1/manual/parallel-computing/),
but this page will describe usage of the distributed memory parallelism provided by the [Distributed](https://docs.julialang.org/en/v1/manual/distributed-computing/) module.

In particular, this is the form of parallelism used for [DifferentialEquations.jl EnsembleDistributed](https://diffeq.sciml.ai/stable/features/ensemble/#EnsembleAlgorithms) calculations,
which is the recommended method for parallel simulations in [NQCDynamics.jl](https://github.com/NQCD/NQCDynamics.jl).
The detailed description in the [Julia manual](https://docs.julialang.org/en/v1/manual/distributed-computing/) describes how to write your own parallel code,
but this guide will focus on how to setup and use parallel code on a Slurm cluster.

## Introduction to Julia multiprocessing

By default, Julia launches with a single process.
Extra processes can be launched by specifying the `-p n` command line argument.
`n` refers to the number of worker processes and should be equal to the number of cores on the machine.

Launching Julia with `julia -p 2`:
```julia
nworkers()
2
```

Alternatively, the number of workers can be modified after launching Julia using `addprocs`:
```julia
using Distributed
addprocs(4)
nworkers()
4
```

> Launching Julia with `-p` implicitly executes `using Distributed`.
> To access the `nworkers` and `addprocs` functions when launched without `-p`,
> include `using Distributed` at the top of your script.

## Using [SlurmClusterManager](https://github.com/kleinhenz/SlurmClusterManager.jl)

On Slurm clusters, there is a simple way to ensure we are running Julia with the correct amount of processes.
[SlurmClusterManager.jl](https://github.com/kleinhenz/SlurmClusterManager.jl) gives us:
```julia
using Distributed, SlurmClusterManager
addprocs(SlurmManager())
```
When this script is launched from within a Slurm submission,
`addprocs(SlurmManager())` will automatically detect the correct amount of processes from the Slurm environment variables defined in the submission script.
This means we only have to specify our allocation in the Slurm script and Julia will sort itself out.

A Slurm script might look something like this, where `my_script.jl` uses [SlurmClusterManager.jl](https://github.com/kleinhenz/SlurmClusterManager.jl)
to ensure Julia uses the correct amount of processes.
```bash
#!/bin/bash
#SBATCH --nodes=16
#SBATCH --time=24:00:00
#SBATCH --ntasks-per-node=128
#SBATCH --cpus-per-task=1

julia my_script.jl
```

## Code loading considerations

Now that we have described how to launch Julia correctly, there are a few extra considerations to ensure your script will execute without issue.

### @everywhere macro
As described in the [Julia manual](https://docs.julialang.org/en/v1/manual/distributed-computing/#code-availability),
by default, any code that is imported or functions that are defined are loaded only on process 1.
This will lead to errors as soon as other processes attempt to execute code that they cannot see.
To fix this, the [@everywhere](https://docs.julialang.org/en/v1/stdlib/Distributed/#Distributed.@everywhere) macro can be used to load code on all processes.
For example, any packages that you want your parallel processes to use should be imported with `@everywhere`:
```julia
using Distributed, SlurmClusterManager
addprocs(SlurmManager())

@everywhere using LinearAlgebra
@everywhere using NQCDynamics
```

> To avoid writing `@everywhere` many times, you can use a `begin ... end` block.
> This is equivalent to the above:
> ```julia
> @everywhere begin
>   using LinearAlgebra
>   using NQCDynamics
> end
> ```

### Julia environments

It is recommended that you use [Pkg environments](https://pkgdocs.julialang.org/v1/environments/) to manage your project dependencies.
As with code loading, the environment must be activated for all processes.
There are a few different ways to do this:

#### Activate the environment inside Julia script

A simple way to do this is to use `Pkg.activate` with `@everywhere`:
```julia
@everywhere begin
    using Pkg
    Pkg.activate("path/to/environment")
end
```

#### Activate the environment from the command line

Alternatively, if you launch Julia with `--project=path/to/env`, you can add the `exeflags="--project"` keyword argument to `addprocs` to
ensure that each process correctly activates the environment. 

```bash
$ julia --project=path/to/env
julia> addprocs(10; exeflags="--project")
```

#### Use `DrWatson.@quickactivate`

Another option is to use [DrWatson](https://juliadynamics.github.io/DrWatson.jl/dev/) to manage your project.
Along with a whole host of project management utilities, it provides the [`@quickactivate` macro](https://juliadynamics.github.io/DrWatson.jl/dev/project/#DrWatson.@quickactivate)
which can be combined with `@everywhere` to quickly activate your project.

## Further reading

With the above instructions you should now be able to execute Julia scripts that use `Distributed` multiprocessing.
For a detailed description of what you can do with this form of parallelism in Julia, refer to the [manual](https://docs.julialang.org/en/v1/manual/distributed-computing/#Multi-processing-and-Distributed-Computing).

