# Hosting binary packages with BinaryBuilder.jl

This guide will describe the process I followed to create [H2AgModel_jll.jl](https://github.com/maurergroup/H2AgModel_jll.jl).
The [BinaryBuilder.jl docs](https://docs.binarybuilder.org/stable/) do a pretty job of explaining what to do but here I'll provide a streamlined experience.

The whole process involves simply creating a `build_tarballs.jl` script and running it from the command line with:

`julia build_tarballs.jl --verbose --debug --deploy="username/repo"`

Many examples of these scripts are available in the [Yggdrasil repo](https://github.com/JuliaPackaging/Yggdrasil) but direct reference to this
is not always very helpful.

The script I created and will be referencing can be seen [here](https://github.com/maurergroup/ML-model-repository/blob/master/H2_on_Ag111/build_tarballs.jl).

Things to include in the script:
1. `name` - This is the name of the package.
2. `version` - The version
3. `sources` - Location of the source code that we will compile. In the case of a `GitSource`, the string of letters you see should be
taken directly from a github commit. You can see this in the top right on Github whenever you are viewing a commit.
We reference a specific commit to ensure it doesn't change as the branch gets updated.
4. `script` - The script executed in order to compile the code. Typically this will just run the make file with `make`, but in my script I have explicitly written everything out. Note that I have moved the outputs into the `${libdir}`, since I have compiled shared libraries. BinaryBuilder.jl automatically provides a few environment variables that you can use, these include all the ones I have used, and you can find the rest in the docs.
5. `platforms` - Determines the platforms to compile on, usually we will only be interested in the linux x86_64 platform. Note that we also use the `expand_gfortran_versions` function, this means we will compile with three different versions of `gfortran` for compatibility reasons.
I found that I could not put too many options for platforms as you need lots of storage space to run the docker environments for many platforms.
6. `products` - Lists the output libraries and binaries, here I have compiled two libraries and include them both here.
I also have some `.txt` files used by the libraries but it's not necessary to include those here.
7. `dependencies` - Labels the library dependencies needed to compile the code. It seems this `CompilerSupportLibraries_jll` is always needed
for fortran code.
8. `build_tarballs` - Executes the build. 

Then finally all you have to do is run the script with the command I mention at the top. It takes quite a while and has to download quite a lot of stuff but should proceed without issue. You will likely have to provide github authorisation which you can do easily by adding a token.
You can generate a token [here](https://github.com/settings/tokens) which you can add to `~/.julia/config/startup.jl` with the line `ENV["GITHUB_TOKEN"] = token`.

After the script runs, it will automatically upload the package to the specified `--deploy="user/repo". To make a new version, just update the version number in the script and run it again.

Hopefully this is everything you need, check the BinaryBuilder.jl docs for more info. They provide information on how to use your new _jll package and give other options for the sources, platforms etc.
