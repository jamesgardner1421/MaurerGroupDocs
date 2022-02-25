# Compiling FHIaims on Sulis

## Step 1: Git clone FHIaims from gitlab

To gain access to the Gitlab, you can e-mail kokott@fhi-berlin.mpg.de or aims-coordinators@fhi-berlin.mpg.de which should get you access after setting up a user account. 

Git clone FHIaims repository `git clone https://aims-git.rz-berlin.mpg.de/aims/FHIaims.git ` 

## Step 2: Compiling FHIaims 

Load the necessary modules - 
```bash
module purge
module load foss/2021b
module load CMake
```

`cd` into the FHIaims directory and issue the following command `mkdir build && cd build`.
Within the `build/` directory `touch initial.cmake` 

### Example of `initial.cmake`

```makefile
set(CMAKE_Fortran_COMPILER mpif90 CACHE STRING "")
set(CMAKE_Fortran_FLAGS "-O3 -ffree-line-length-none -fallow-argument-mismatch " CACHE STRING "")
set(Fortran_MIN_FLAGS "-O0 -fbacktrace -ffree-line-length-none -fallow-argument-mismatch " CACHE STRING "")
set(LIBS "scalapack openblas" CACHE STRING "" FORCE)
set(USE_MPI ON CACHE BOOL "" FORCE)
set(USE_SCALAPACK ON CACHE BOOL "" FORCE)
set(USE_LIBXC ON CACHE BOOL "" FORCE)
set(USE_HDF5 OFF CACHE BOOL "" FORCE)
set(USE_RLSY ON CACHE BOOL "" FORCE)
set(CMAKE_C_COMPILER mpicc CACHE STRING "")
set(CMAKE_C_FLAGS "-O3 -funroll-loops -std=gnu99" CACHE STRING "")
```

Next issue the command `cmake -C initial.cmake ../.` - after this is complete output then issue the next command `make -j 8` - NB: the number refers to the number of processes on that machine. 
The compiled code `aims.$VERSION.scalapack.mpi.x` will be in `build/` directory. 


# Bash-submit script:

```bash
#!/bin/bash
#SBATCH --job-name=XXX
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=128
#SBATCH --mem-per-cpu=3850
#SBATCH --time=24:00:00
#SBATCH --account=XXX

module purge
module load foss/2021b

export OMP_NUM_THREADS=1
export MKL_NUM_THREADS=1
export MKL_DYNAMIC=FALSE

ulimit -s unlimited

bin='/Path/to/Executable/aims.${VERSION}.scalapack.mpi.x'

srun $bin > aims.out
```


