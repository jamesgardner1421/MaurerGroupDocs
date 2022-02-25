# Setup Julia on HPC systems

This guide is based upon the information directly provided by the [Julia website](https://julialang.org/). To make this guide more useful I will focus on HPC installation, though the setup is very similar across platforms.

## Download and install Julia
Julia can be directly obtained from the [website](https://julialang.org/downloads/). Get the current stable release and follow the [platform specific instructions](https://julialang.org/downloads/platform/).

On Linux, you should use `wget` to download the tarball and extract using `tar -zxvf`. Next, you should ensure that the `julia` binary is added to the `PATH` environment variable.

> I keep two top level directories for storing programs, `~/programs` and `~/bin`, where `~/bin` is a directory added to the path within the `.bash_profile`. I put the `julia-x.y.z` directory inside `~/programs` and create a symbolic link (`ln -s ~/programs/julia-x.y.z/bin/julia ~/bin/julia`) to the `~/bin` directory. The advantage of this method is that only `~/bin` needs to be added to the `PATH`.
> Any time you need a new program you can follow this pattern and things remain organised.

At this point you should be able to type `julia` and have the REPL appear before your very eyes.

## Running scripts on a cluster

Running Julia scripts works in the same way as for python, you set up a slurm script with all the necessary `#SBATCH` commands and include `julia my_script.jl`.

Your slurm script might look a little something like this:
```bash
#!/bin/bash
#SBATCH --ntasks=1
#SBATCH --time=24:00:00
#SBATCH --mem-per-cpu=2012

julia my_script.jl
```

## Packages, packages, packages...

Julia has a super handy package manager [`Pkg`](https://docs.julialang.org/en/v1/stdlib/Pkg/), it is important to understand how `Pkg` and the associated environments work in order to use code not provided within the Julia Base library. I highly recommend taking a quick look at the [documentation](https://docs.julialang.org/en/v1/stdlib/Pkg/).

It is suggested that you create a new environment for each project and you should ensure this environment has been activated for your script. This can be done by running julia with `julia --project=/path/to/myenv my_script.jl`.
Now your script will be able to access any package that you have installed into your project environment.

## Parallelism

The list of [command-line options](https://docs.julialang.org/en/v1/manual/command-line-options/) mentions the `-t` and `-p` options. These allow the specification of the number of threads and processes to use.

## Things to add

If anyone wants anything extra included in this guide, feel free to add it or ask me (james). At some point I will merge the existing VScode guide I made with this one.
