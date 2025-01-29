### Molecular Dynamics

#### GROMACS

##### Introduction

GROMACS is a versatile package to perform molecular dynamics, i.e. simulate the Newtonian equations of motion for systems with hundreds to millions of particles, and is a community-driven project. It is primarily designed for biochemical molecules like proteins, lipids, and nucleic acids that have a lot of complicated bonded interactions, but since GROMACS is extremely fast at calculating the nonbonded interactions (that usually dominate simulations) many groups are also using it for research on non-biological systems, e.g. polymers and fluid dynamics.

The official website for GROMACS: [https://www.gromacs.org/](https://www.gromacs.org/)


##### Input

The following example illustrates the running of GROMACS with the commonly used water_GMX50_bare benchmark data set.

Running GROMACS is a two-step process:

1. Generating the input file (.tpr)
2. Running the generated input files (.tpr)

`wget http://ftp.gromacs.org/pub/benchmarks/water_GMX50_bare.tar.gz`

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="rfm_job"
#SBATCH --ntasks=48
#SBATCH --ntasks-per-node=48
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=cpu
spack load gromacs/m6krxp7
spack load openmpi/qmzsyj6
tar -xvf water_GMX50_bare.tar.gz
cd water-cut1.0_GMX50_bare/3072
gmx_mpi grompp -f pme.mdp -c conf.gro -p topol.top -o water_pme.tpr
time \
mpirun -np 48 gmx_mpi  mdrun -nsteps 5000 -s water_pme.tpr
```


#### LAMMPS

##### Introduction

LAMMPS stands for Large-scale Atomic/Molecular Massively Parallel

Simulator.

The official website for LAMMPS: [https://www.lammps.org/](https://www.lammps.org/)

##### Input

The following example illustrates the running of LAMMPS with the commonly used data set.

Input Data Set link: [https://www.lammps.org/bench/inputs/in.lj.txt](https://www.lammps.org/bench/inputs/in.lj.txt)

```
# 3d Lennard-Jones melt
variable	x index 1
variable	y index 1
variable	z index 1
variable	xx equal 20*$x
variable	yy equal 20*$y
variable	zz equal 20*$z
units		lj
atom_style	atomic
lattice		fcc 0.8442
region		box block 0 ${xx} 0 ${yy} 0 ${zz}
create_box	1 box
create_atoms	1 box
mass		1 1.0
velocity	all create 1.44 87287 loop geom
pair_style	lj/cut 2.5
pair_coeff	1 1 1.0 1.0 2.5
neighbor	0.3 bin
neigh_modify	delay 0 every 20 check no
fix		1 all nve
run		100000
```

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="rfm_job"
#SBATCH --ntasks=192
#SBATCH --ntasks-per-node=48
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=hm
spack load lammps/feqr35c
spack load intel-oneapi-mpi/3alw73q
export OMP_NUM_THREADS=1
time \
mpirun -np 192 lmp  -in in.lj.txt
```


#### NAMD

##### Introduction

NAMD is a parallel molecular dynamics code designed for high-performance

simulation of large biomolecular systems.

The official website for NAMD: [https://www.ks.uiuc.edu/Research/namd/](https://www.ks.uiuc.edu/Research/namd/)

##### Input

The following example illustrates the running of NAMD with the commonly used data set.

Input Data Set link: [https://www.ks.uiuc.edu/Research/namd/utilities/apoa1.tar.gz](https://www.ks.uiuc.edu/Research/namd/utilities/apoa1.tar.gz)

`wget https://www.ks.uiuc.edu/Research/namd/utilities/apoa1.tar.gz`

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="rfm_job"
#SBATCH --ntasks=48
#SBATCH --ntasks-per-node=48
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=cpu
spack load namd/ynfxnux
export OMP_NUM_THREADS=1
tar -xvf apoa1.tar.gz
cd apoa1
time \
mpirun -np 48 namd2 apoa1.namd &> output
```


<!-- #### GAMMES

#### SIESTA

#### CHARMM

#### AMBER

#### HOOMD-blue -->
