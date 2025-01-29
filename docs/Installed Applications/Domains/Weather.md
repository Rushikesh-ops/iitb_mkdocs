# Weather

#### ROMS

##### Introduction

ROMS solves the free-surface, hydrostatic, flux form of the primitive equations over variable bathymetry using stretched terrain following in the vertical and orthogonal curvilinear coordinates in the horizontal. The finite volume grid is discretized on a staggered Arakawa C-grid.


##### Input
Dataset location – PARAM Rudra IUAC, Delhi
/home/apps/hpc_inputs/ROMS

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="rfm_job"
#SBATCH --ntasks=96
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=hm
 
spack load intel-oneapi-mpi/bzriwnc
spack load intel-oneapi-compilers@2023.2.0
export OMP_NUM_THREADS=1
#wget https://www2.mmm.ucar.edu/wrf/users/benchmark/v422/v42_bench_conus2.5km.tar.gz
ulimit -s unlimited
ml load apps/roms
cd run
time \
mpirun -np 96 roms
```