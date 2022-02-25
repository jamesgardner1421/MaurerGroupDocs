# Installation GPAW
## Before Installation
### Modify your .bashrc
First, note that you need to do the compilation on the `/work` partition, so the executables will be available from the computing notes.

You should also export the following to your `bashrc` (`user` is your username. `E635` needs to be adjusted according to whatever partition you have on Archer):

```bash
export PATH=/work/E635/E635/user/.local/bin:$PATH
export PYTHONUSERBASE=/work/E635/E635/user/.local
export PATH=$PYTHONUSERBASE/bin:$PATH
export PYTHONPATH=/work/E635/E635/user/.local:$PYTHONPATH
```

### The Installation Environment
Load the necessary modules:

```bash
module restore PrgEnv-gnu
module load cray-python
```

## Installation of Ase and Libxc
1. Install `ase` with `pip install ase` on the /work partition
2. Install libxc by downloading the latest version from the libxc home (`git clone https://gitlab.com/libxc/libxc`). 
You can either compile it with make: 

   ```bash
   ./configure --prefix=/work/E635/E635/user/code_versions/GPAW/libxc/libxc-5.1.2 CC=cc CFLAGS=-fPIC FC=ftn
   ```
   (`--prefix` is followed by the path where you want to install libex). If the `configure` file does not appear in your `libxc/` directory, you will need to run `autoreconf -i` to generate it (see the `README` file).
   
   ```bash
   make -j 16
   make install
   ```

   Alternatively, you can use cmake, which is useful in case you also want to use libxc in other python codes:
   ```bash
   cmake -H. -Bobjdir -DCMAKE_INSTALL_PREFIX=/work/E635/E635/user/code_versions/GPAW/libxc/libxc-5.1.2/bin1
   cd objdir
   make -j16 install
   ```

## Installation gpaw
1. git clone gpaw: `git clone -b 21.1.0 https://gitlab.com/gpaw/gpaw.git` (note: check if the version has changed, -b 21.1.0 is the current version as I'm writing this)
2. In the gpaw directory, `cp siteconfig_example.py siteconfig.py`
3. Open siteconfig.py, set `scalapack =True` and comment out the next two lines. Add the following lines in the beginning of the document:
   ```python
   library_dirs += ['/home/E635/E635/sjanke/code_versions/GPAW/libxc/libxc-5.1.2/lib']
   include_dirs += ['/home/E635/E635/sjanke/code_versions/GPAW/libxc/libxc-5.1.2/include']
   compiler = 'cc'
   mpicompiler = 'cc'
   mpilinker='cc'
   extra_compile_args =['-fPIC']
   ```

4. Build everything: `python setup.py build`

5. Install gpaw: `python setup.py install --user` (`--user` will install gpaw into you /work/E.../E.../user/.local directory)

6. Get the tar-ball with the current PAW from the GPAW webpage and untar it in a sensible directory.

Your gpaw should now be ready to run.

# The job.sh file
The job file needs to include the following lines to be able to run the code:

```bash
module load epcc-job-env
module load cray-python
```

The paths:

```bash
export PYTHONUSERBASE=/work/E635/E635/user/.local
export PATH=$PYTHONUSERBASE/bin:$PATH
```

And the directory of the untared PAWs:

```bash
export GPAW_SETUP_PATH=/work/E635/E635/user/gpaw_bin/GPAW_setup/gpaw-setups-0.9.20000
```

Finally, the submission line in the job.sh file would look like this:

```bash
srun --distribution=block:block --hint=nomultithread /work/E635/E635/user/.local/bin/gpaw python exe.py
```

# Regression tests
To run the regression tests, copy into a convenient location or go into `gpaw/test` from you gpaw directory.

Note that the regression tests are supposed to be run serially, so make sure you only have one core in your job.sh file (`#SBATCH --tasks-per-node=1`). The tests can be run with:

```bash
srun --distribution=block:block --hint=nomultithread pytest -v
```