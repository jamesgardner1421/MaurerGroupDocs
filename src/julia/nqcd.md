# Contributing to NQCD

<p align="right">
  <a href="https://nqcd.github.io/NQCDynamics.jl/stable/">
    <img src="https://github.com/NQCD/NQCDLogo/blob/main/images/logo_with_text.png" alt="NQCDynamics.jl logo"
         title="NQCDynamics.jl" align="right" height="150"/>
  </a>
</p>

This page describes the process of contributing to NQCD for members of the maurergroup.
In particular, it focuses on the case where your contributions should be kept private until ready for publication.
For general guidelines for contributing to open source Julia packages, refer to the [Contributor's Guide](https://github.com/SciML/ColPrac) from the SciML organisation.

## NQCD overview

The components of NQCD are distributed over multiple repositories hosted within the [NQCD organisation on GitHub](https://github.com/NQCD).
Most of these repositories are [public](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility),
which means anyone can see all of the branches pushed to the remote repository.
Often we want to keep our developments private whilst we work on them which means we cannot use the public repositories to store our development work.

## Handling private development

There are two options that allow us to develop in private. If you are not sure which is more appropriate for your feature, feel free to ask.

### 1. Private add-on packages

The modular nature of Julia and the NQCD project allows us to split up the code into multiple packages without any downsides. In fact, it is often advantageous as it allows you to add as many dependencies as you like and you are able to host your own test sets.
This set up is desirable when developing a new model built on top of [NQCModels.jl](https://github.com/NQCD/NQCModels.jl) or an interface that requires extra dependencies or is relatively complex.
To do this, simply [create a new GitHub repository](https://docs.github.com/en/get-started/quickstart/create-a-repo) and generate a new Julia package.
For this, I recommend using [PkgTemplates.jl](https://github.com/invenia/PkgTemplates.jl).
Examples of the add-on setup include: [TullyNOAu111.jl](https://github.com/NQCD/TullyNOAu111.jl) and [NNInterfaces.jl](https://github.com/NQCD/NNInterfaces.jl), along with many of the packages you can find [here](https://github.com/NQCD).
Technically, you are free to host these packages wherever you like, either within NQCD, maurergroup or even on your own profile.
Though the best place is probably NQCD so we can manage it more easily.
The key is that you make sure the visibility of the repository is set to private.

### 2. Private "forks" of public repositories

If you development is more suitable within one of the existing public packages, then we must employ an alternate strategy.
We cannot publish our changes to the public repository, so we must create a private copy of the repository.
Usually when you want to create a copy of a repository, you would [fork the repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo) which has the advantage of closely linking the repositories automatically, making it easy to synchronise and publish changes.
However, GitHub does not allow for private forks of public repositories which means we must find another way.
Fortunately, GitHub provides instructions for how to [duplicate a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository). We can use this to create a private copy of the public repository and manually handle the synchronisation between them.

#### Development procedure

The development of a private feature for a public repository is as follows:

1. Create a private copy of the main repository if it does not already exist. ([Duplicating a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository))
2. Create a new branch off of `main` named `{feature-name}-staging`.
This will act as the effective `main` branch for your new development and will never be merged into the actual `main`.
3. To work on your feature, you should create another branch off of `{feature-name}-staging` and open up a [draft pull request](https://github.blog/2019-02-14-introducing-draft-pull-requests/).
4. Commit as many changes as you like to your branch. These changes will appear in the pull request and the automated tests will run.
5. Merge your development branch into `{feature-name}-staging` when you are ready.
6. Repeat steps 3, 4 and 5 until your feature is complete.
7. When ready for public release, push your `{feature-name}-staging` branch to the public repository and open a pull request.
This can be merged straight away since it should be in perfect condition by this stage.

#### Extra considerations

The `main` branch of the private repository should exactly copy and be kept in sync with the `main` branch of the public repository. This can be done using GitHub actions or manually.
The private `main` branch acts only as a copy of the public repository so it is simpler to keep private developments up to date with the public repository.
Periodically, you should merge/rebase `main` into your `{feature-name}-staging` branch to ensure your developments do not fall out of sync with other changes.

It is recommended that the steps 3-6 are comprised of many small branches with detailed discussion in each pull request. This way we can keep things moving and are less likely to end up out of sync with other developments.

This process assumes that each feature is independent and the features do not interact with one another.
For most cases this will be true so it should be possible to have many features with their own `{feature-name}-staging` branches all in development at the same time.
