# Installed Applications/Libraries
<!-- # Installed Applications/Libraries
## Compilers
### GNU compilers
### Nvidia Compilers
### Intel Compilers
### OneAPI Compilers

<hr>

## Libraries
### MPI (Message Passing Interface) 
#### OpenMPI
#### MPICH
#### Intel MPI
#### MVAPICH2
#### HPCX-MPI
#### Intel OneAPI MPI

### FFTW
### NetCDF
### HDF5
### SCOTCH
### PT-SCOTCH
### METIS
### OpenBLAS
### Intel MKL
### KAHIP
### LAPACK
### SCALAPACK
### EIGEN
### BOOST
### HYPRE
### PETSc
### MAGMA
### CUDA
### AMREX
### PLUMED



<hr>

## BenchMarks
### HPL
### HPCG
### OSU MicroBenchmarks
### IOR
### STREAM
### MLperf
### RESNET50
### AMG-2023

<hr>

## Domains

### Molecular Dynamics

#### GROMACS
#### LAMMPS
#### NAMD
#### GAMMES
#### SIESTA
#### CHARMM
#### AMBER
#### HOOMD-blue

<hr>

### Computational Fluid Dynamics
#### OpenFOAM
#### SU2
#### CFDEM
#### FDS


### Material Science
#### ABINIT
#### VASP
#### Quantum Espresso
#### CP2K

### Bio-Informatics
#### Galaxy

### Computational Chemistry
#### NWChem
#### OpenMolcas


### Weather/Climate Modelling
#### CFS
#### WRF
#### ROMS
#### MOMS


### Earth Science
#### ANUGA

### Astrophysics
#### PyconGPU
#### AMBERX-CASTRO
#### 

<hr>

## Visulization Tools

### ParaView
### VMD
### Blender
### Matplotlib
### JMOl
### RedMD
### VisIt
### GNUPlot



<hr> -->


Following is the list of few of the applications from various domains of science and engineering installed in the system.

<table>
    <tr>
        <td>HPC Applications</td>
        <td>Bio-informatics</td>
        <td>MUMmer, HMMER, MEME, Schrodinger, PHYLIP, mpiBLAST, ClustalW, </td>
    </tr>
    <tr>
        <td> </td>
        <td>Molecular Dynamics</td>
        <td>NAMD (for CPU and GPU), LAMMPS, GROMACS </td>
    </tr>
    <tr>
        <td> </td>
        <td>Material Modeling, Quantum Chemistry</td>
        <td>Quantum-Espresso, Abinit, CP2K, NWChem, </td>
    </tr>
    <tr>
        <td> </td>
        <td>CFD</td>
        <td>OpenFOAM, SU2 </td>
    </tr>
    <tr>
        <td> </td>
        <td>Weather, Ocean, Climate</td>
        <td>WRF-ARW, WPS (WRF), ARWPost (WRF), RegCM, MOM, ROMS </td>
    </tr>
    <tr>
        <td>Deep Learning Libraries</td>
        <td>cuDNN, TensorFlow,  Tensorflow with Intel Python , Tensorflow with GPU, Theano, Caffe , Keras ,  numpy, Scipy, Scikit-Learn, pytorch. </td>
        <td></td>
    </tr>
    <tr>
        <td>Visualization Programs</td>
        <td>GrADS, ParaView, VisIt, VMD </td>
        <td></td>
    </tr>
    <tr>
        <td>Dependency Libraries</td>
        <td>NetCDF, PNETCDF, Jasper, HDF5, Tcl, Boost, FFTW</td>
        <td></td>
    </tr>
</table>


## Standard Application Programs on PARAM Rudra

The purpose of this section is to expose the users to different application packages which have been installed on PARAM Rudra System. Users interested in exploring these packages may kindly go through the scripts, typical input files and typical output files. It is suggested that at first, the users may submit the scripts provided and get a feel of executing the codes. Later, they may change the parameters and the script to meet their application requirements.


## LAMMPS Applications

LAMMPS is an acronym for Large-scale Atomic/ Molecular Massively Parallel Simulator. This is extensively used in the fields of Material Science, Physics, Chemistry and many others. More information about LAMMPS may please be found at [https://lammps.sandia.gov](https://lammps.sandia.gov) .

⦁	The LAMMPS input is in.lj  file which contains the below parameters.
```
Input file = in.lj 

# 3d Lennard-Jones melt
variable        x index 1
variable        y index 1
variable        z index 1
variable        xx equal 64*$x
variable        yy equal 64*$y
variable        zz equal 64*$z
units           lj
atom_style      atomic
lattice         fcc 0.8442
region          box block 0 ${xx} 0 ${yy} 0 ${zz}
create_box      1 box
create_atoms    1 box
mass            1 1.0
velocity        all create 1.44 87287 loop geom
pair_style      lj/cut 2.5
pair_coeff      1 1 1.0 1.0 2.5
neighbor        0.3 bin
neigh_modify    delay 0 every 20 check no

fix             1 all nve
run             1000000
```


- THE LAMMPS RUNNING SCRIPT

```
#!/bin/sh
#SBATCH -N 8
#SBATCH --ntasks-per-node=40
#SBATCH --time=08:50:20
#SBATCH --job-name=lammps
#SBATCH --error=job.%J.err_8_node_40
#SBATCH --output=job.%J.out_8_node_40
#SBATCH --partition=standard
spack load intel-oneapi-compilers /jtvke3n
spack load intel-oneapi-mpi/2db2e7t
spack load gcc@13.2.0/3wdooxp
source /home/apps/SPACK/spack/opt/spack/linux-almalinux8-cascadelake/gcc-13.2.0/intel-oneapi-mkl-2024.0.0-yq4keqsjr44rf5ffroiiim2iklxg4let/setvars.sh intel64
export I_MPI_FALLBACK=disable
export I_MPI_FABRICS=shm:ofa
#export I_MPI_FABRICS=shm:tmi
#export I_MPI_FABRICS=shm:dapl
export I_MPI_DEBUG=5
#Enter your working directory or use SLURM_SUBMIT_DIR
cd /home/manjunath/NEW_LAMMPS/lammps-7Aug19/bench

export OMP_NUM_THREADS=1
time mpiexec.hydra -n $SLURM_NTASKS -genv OMP_NUM_THREADS 1 <path of lammps executable> -in in.lj
```

- LAMMPS OUTPUT FILE.

```
LAMMPS (7 Aug 2019)
  using 1 OpenMP thread(s) per MPI task
Lattice spacing in x,y,z = 1.6796 1.6796 1.6796
Created orthogonal box = (0 0 0) to (107.494 107.494 107.494)
  5 by 8 by 8 MPI processor grid
Created 1048576 atoms
  create_atoms CPU = 0.00387692 secs
Neighbor list info ...
  update every 20 steps, delay 0 steps, check no
  max neighbors/atom: 2000, page size: 100000
  master list distance cutoff = 2.8
  ghost atom cutoff = 2.8
  binsize = 1.4, bins = 77 77 77
  1 neighbor lists, perpetual/occasional/extra = 1 0 0
  (1) pair lj/cut, perpetual
      attributes: half, newton on
      pair build: half/bin/atomonly/newton
      stencil: half/bin/3d/newton
      bin: standard
Setting up Verlet run ...
  Unit style    : lj
  Current step  : 0
  Time step     : 0.005
Per MPI rank memory allocation (min/avg/max) = 3.154 | 3.156 | 3.162 Mbytes
Step Temp E_pair E_mol TotEng Press
       0         1.44   -6.7733681            0   -4.6133701   -5.0196704
 1000000   0.65684946   -5.7123998            0   -4.7271266   0.49078272
Loop time of 2955.97 on 320 procs for 1000000 steps with 1048576 atoms
Performance: 146145.063 tau/day, 338.299 timesteps/s
99.4% CPU use with 320 MPI tasks x 1 OpenMP threads
MPI task timing breakdown:
Section |  min time  |  avg time  |  max time  |%varavg| %total
---------------------------------------------------------------
Pair    | 1284.2     | 1512.3     | 1866.9     | 494.3 | 51.16
Neigh   | 178.94     | 207.58     | 261.09     | 217.8 |  7.02
Comm    | 793.59     | 1207.7     | 1468.3     | 654.3 | 40.86
Output  | 0.00011516 | 0.00084956 | 0.0027411  |   0.0 |  0.00
Modify  | 19.566     | 22.639     | 29.863     |  67.3 |  0.77
Other   |            | 5.744      |            |       |  0.19
Nlocal:    3276.8 ave 3325 max 3231 min
Histogram: 4 7 21 63 67 80 50 22 5 1
Nghost:    5011.29 ave 5063 max 4956 min
Histogram: 5 9 26 45 57 76 51 34 12 5
Neighs:    122781 ave 127005 max 118605 min
Histogram: 3 5 36 59 63 52 66 24 11 1

Total # of neighbors = 39290074
Ave neighs/atom = 37.4699
Neighbor list builds = 50000
Dangerous builds not checked
Total wall time: 0:49:15
```



## GROMACS APPLICATION

##### GROMACS

GROningen MAchine for Chemical Simulations (GROMACS) is a [molecular dynamics](https://en.wikipedia.org/wiki/Molecular_dynamics) package mainly designed for simulations of [proteins](https://en.wikipedia.org/wiki/Protein), [lipids](https://en.wikipedia.org/wiki/Lipid), and [nucleic acids](https://en.wikipedia.org/wiki/Nucleic_acid). It was originally developed in the Biophysical Chemistry department of [University of Groningen](https://en.wikipedia.org/wiki/University_of_Groningen), and is now maintained by contributors in universities and research centres worldwide. GROMACS is one of the fastest and most popular software packages available, and can run on [central processing units](https://en.wikipedia.org/wiki/Central_processing_unit) (CPUs) and [graphics processing units](https://en.wikipedia.org/wiki/Graphics_processing_unit) (GPUs).

Input description of Gromacs

Input file can be download from [ftp://ftp.gromacs.org/pub/benchmarks/water_GMX50_bare.tar.gz](ftp://ftp.gromacs.org/pub/benchmarks/water_GMX50_bare.tar.gz)

The mdp option used is pme with 50000 steps 

Submission Script:
```
#!/bin/sh
#SBATCH -N 10
#SBATCH --ntasks-per-node=48
##SBATCH --time=03:05:30
#SBATCH --job-name=gromacs
#SBATCH --error=job.16.%J.err
#SBATCH --output=job.16.%J.out
#SBATCH --partition=standard

source /home/apps/SPACK/spack/share/spack/setup-env.sh

spack load intel-oneapi-compilers /jtvke3n
spack load gromacs@5.1.4 /73dy73q

#Enter your working directory or use SLURM_SUBMIT_DIR
cd /home/shweta/water-cut1.0_GMX50_bare/3072

export I_MPI_DEBUG=5
export OMP_NUM_THREADS=1
mpirun -np 4 gmx_mpi  grompp -f pme.mdp  -c conf.gro -p topol.top

time mpirun -np $SLURM_NTASKS gmx_mpi mdrun -s topol.tpr) 2>&1 | tee log_gromacs_40_50k_mpirun
```

Output Snippet:
```
Number of logical cores detected (48) does not match the number reported by OpenMP (1).
Consider setting the launch configuration manually!
Running on 10 nodes with total 192 cores, 480 logical cores
  Cores per node:            0 - 48
  Logical cores per node:   48
Hardware detected on host cn072 (the node of MPI rank 0):
  CPU info:
    Vendor: GenuineIntel
    Brand:  Intel(R) Xeon(R) Platinum 8268 CPU @ 2.90GHz
    SIMD instructions most likely to fit this hardware: AVX2_256
    SIMD instructions selected at GROMACS compile time: AVX2_256
Reading file /home/shweta/Gromacs/water-cut1.0_GMX50_bare/3072/topol.tpr, VERSION 5.1.4 (single precision)
Changing nstlist from 10 to 20, rlist from 1 to 1.032
The number of OpenMP threads was set by environment variable OMP_NUM_THREADS to 1 (and the command-line setting agreed with that)
NOTE: KMP_AFFINITY set, will turn off gmx mdrun internal affinity
      setting as the two can conflict and cause performance degradation.
      To keep using the gmx mdrun internal affinity setting, set the
      KMP_AFFINITY=disabled environment variable.
Overriding nsteps with value passed on the command line: 50000 steps, 100 ps
Will use 360 particle-particle and 120 PME only ranks
This is a guess, check the performance at the end of the log file
Using 480 MPI processes
Using 1 OpenMP thread per MPI process
Back Off! I just backed up ener.edr to ./#ener.edr.2#
starting mdrun 'Water'
50000 steps,    100.0 ps.

 Average load imbalance: 5.5 %
 Part of the total run time spent waiting due to load imbalance: 3.0 %
 Average PME mesh/force load: 1.252
 Part of the total run time spent waiting due to PP/PME imbalance: 13.2 %
NOTE: 13.2 % performance was lost because the PME ranks
      had more work to do than the PP ranks.
      You might want to increase the number of PME ranks
      or increase the cut-off and the grid spacing.
               Core t (s)   Wall t (s)        (%)
       Time:   204872.624      427.847    47884.5
                 (ns/day)    (hour/ns)
Performance:       20.195        1.188
```


