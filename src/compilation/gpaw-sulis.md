# Installation GPAW
## Before Installation
### Modify your .bashrc

You should also export the following to your `bashrc` (`user` is your username. fasfddf):

```bash
export PATH=/home/s/user/.local/bin:$PATH
export PYTHONUSERBASE=/home/s/user/.local
export PATH=$PYTHONUSERBASE/bin:$PATH
export PYTHONPATH=/home/s/user/.local:$PYTHONPATH
```

### The Installation Environment
Load the necessary modules:

```bash
module load foss/2020b
module load Python
```

## Installation of Ase and Libxc
1. Install `ase` with `pip install ase` on the /work partition
2. Install libxc by downloading the latest version from the libxc home (`git clone https://gitlab.com/libxc/libxc`). 
You can either compile it with make: 

   `./configure --prefix=/home/s/user/code_versions/libxc-5.1.2 CC=cc CFLAGS=-fPIC FC=gfortran` (`--prefix` is followed by the path where you want to install libex). If the `configure` file does not appear in your `libxc/` directory, you will need to run `autoreconf -i` to generate it (see the `README` file).
   
   `make -j 8`
   
   `make install`

## Installation gpaw
1. git clone gpaw: `git clone -b 21.1.0 https://gitlab.com/gpaw/gpaw.git` (note: check if the version has changed, -b 21.1.0 is the current version as I'm writing this)
2. In the gpaw directory, `cp siteconfig_example.py siteconfig.py`
3. Open siteconfig.py, Add the following lines in the beginning of the document:
```python
library_dirs += ['/home/s/user/code_versions/libxc-5.1.2/lib']
include_dirs += ['/home/s/user/code_versions/libxc-5.1.2/lib']
compiler = 'gcc'
mpicompiler = 'mpicc'
mpilinker='mpicc'
extra_compile_args =['-fPIC']
```

Set `scalapack = True` and modify within following if-statement `libraries += ['scalapack', 'openblas']`.

4. Build everything: `python setup.py build`

5. Install gpaw: `python setup.py install --user` (`--user` will install gpaw into you ~/.local directory)

6. Get the tar-ball with the current PAW from the GPAW webpage and untar it in a sensible directory.

Your gpaw should now be ready to run.

## Job submit script
The Job submit script for gpaw looks for example like this:

```bash
#!/bin/bash

#SBATCH --nodes=1
#SBATCH --ntasks-per-node=4
#SBATCH --mem-per-cpu=3850
#SBATCH --time=00:05:00
#SBATCH --account=xxx


module purge
module load foss/2020b
module load Python/3.8.6


export PYTHONUSERBASE=/home/s/user/.local
export PATH=$PYTHONUSERBASE/bin:$PATH
#Path to PAWs:
export GPAW_SETUP_PATH=/home/s/user/code_versions/GPAW_paw/gpaw-setups-0.9.20000

srun python exe.py
```