# Compiling FHIaims on Avon

## Step 1: Git clone FHIaims from gitlab

To gain access to the Gitlab, you can e-mail kokott@fhi-berlin.mpg.de or aims-coordinators@fhi-berlin.mpg.de which should get you access after setting up a user account. 

Once you have access, 
1. SSH into Avon HPC `ssh username@avon.scrtp.warwick.ac.uk`
2. Git clone FHIaims repository `git clone https://aims-git.rz-berlin.mpg.de/aims/FHIaims.git ` 

## Step 2: Compiling FHIaims 

Load the necessary modules - NB: Avon uses a different intel convention to previous use on older HPC i.e., Orac or Tinis.
```bash
module load iccifort/2019.5.281 impi/2019.7.217 imkl/2019.5.281
module load GCCcore/9.3.0
module load CMake/3.16.4
```

`cd` into the FHIaims directory and issue the following command `mkdir build && cd build`.
Within the `build/` directory `touch initial.cmake` 

### Example of `initial.cmake`

```makefile
set(CMAKE_Fortran_COMPILER mpiifort CACHE STRING "" FORCE)
set(CMAKE_Fortran_FLAGS "-O3 -fp-model precise" CACHE STRING "" FORCE)
set(Fortran_MIN_FLAGS "-O0 -fp-model precise" CACHE STRING "" FORCE)
set(LIB_PATHS "$ENV{MKLROOT}/lib/intel64" CACHE STRING "" FORCE)
set(LIBS "mkl_intel_lp64 mkl_sequential mkl_core mkl_blacs_intelmpi_lp64 mkl_scalapack_lp64" CACHE STRING "" FORCE)
set(CMAKE_C_COMPILER icc CACHE STRING "" FORCE)
set(CMAKE_C_FLAGS "-O3 -ip -fp-model precise" CACHE STRING "" FORCE)
```

Next issue the command `cmake -C initial.cmake. ../.` - after this is complete output then issue the next command `make -j 8` - NB: the number refers to the number of processes on that machine. 
The compiled code `aims.$VERSION.scalapack.mpi.x` will be in `build/` directory. 
