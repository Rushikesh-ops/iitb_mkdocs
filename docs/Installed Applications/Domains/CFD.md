### [Computational Fluid Dynamics](http://127.0.0.1:8000/Installed%20Applications/Domains/Page94-InstalledAppl/)

#### OpenFOAM

##### Introduction

OpenFOAM is a GPL-opensource C++ CFD-toolbox. This offering is supported

by OpenCFD Ltd, producer and distributor of the OpenFOAM software via

[www.openfoam.com](www.openfoam.com), and owner of the OPENFOAM trademark. OpenCFD Ltd has

been developing and releasing OpenFOAM since its debut in 2004.

The official website for OpenFOAM:  [https://www.openfoam.com/](https://www.openfoam.com/)

##### Input

The following example illustrates the running of NWChem with the commonly used data set.

Input Data Set link:[ http://openfoamwiki.net/images/6/62/Motorbike_bench_template.tar.gz](http://openfoamwiki.net/images/6/62/Motorbike_bench_template.tar.gz)

`Wget http://openfoamwiki.net/images/6/62/Motorbike_bench_template.tar.gz`

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="rfm_job"
#SBATCH --ntasks=96
#SBATCH --ntasks-per-node=48
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=cpu
spack load openfoam/ggn7wsm
spack load intel-oneapi-mpi/3alw73q
export OMP_NUM_THREADS=1
tar --strip-components 2 -xf Motorbike_bench_template.tar.gz bench_template/basecase
./Allclean
sed -i -- "s/method .*/method          scotch;/g" system/decomposeParDict
sed -i -- "s/numberOfSubdomains .*/numberOfSubdomains 96;/g" system/decomposeParDict
sed -i -- 's/    #include "streamLines"//g' system/controlDict
sed -i -- 's/    #include "wallBoundedStreamLines"//g' system/controlDict
sed -i -- 's|caseDicts|caseDicts/mesh/generation|' system/meshQualityDict
./Allmesh
time \
mpirun -np 96 simpleFoam -parallel
```

<!-- #### SU2

#### CFDEM

#### FDS -->

#### ANUGA

##### Introduction

The ANUGA Hydrodynamic Model is an open-source software used primarily for simulating the impact of tsunamis, floods, and storm surges in coastal and riverine areas. Developed by the Australian National University (ANU) and Geoscience Australia, ANUGA employs the shallow water wave equations to model the flow of water over complex terrain. The model is widely used for disaster management and planning, allowing detailed simulations of water behavior and predictions in potential flood zones.

##### Input

The dataset represents the geographic area of the Mahanadi Delta, India, with actual inputs including tidal data, rainfall data, mesh data, ALOS data, and other necessary inputs. This data uses records from September 12 for simulation purposes. Input files are organized in respective directories in CSV and other compatible formats.

##### Slurm Script

```
#!/bin/bash                     # Specifies that the script should be executed with the bash shell.

#SBATCH --nodes=4               # Requests 4 compute nodes for the job.
#SBATCH --ntasks-per-node=46    # Requests 46 tasks per node (assuming a maximum of 48 cores).
#SBATCH -p standard             # Specifies the partition/queue to be used, such as 'standard' or 'gpu'.
#SBATCH --time=2-00:00:00       # Sets the maximum job runtime to 2 days.
##SBATCH --output=mahanadi_delta_.%J.out # Specifies the output file name pattern, using job ID.
#SBATCH --exclusive             # Requests exclusive access to nodes for optimal resource use.

echo "SLURM_JOBID="$SLURM_JOBID          # Prints the SLURM job ID for reference.
echo "SLURM_JOB_NODELIST"=$SLURM_JOB_NODELIST # Shows the list of nodes assigned to the job.
echo "SLURM_NNODES"=$SLURM_NNODES        # Prints the number of nodes allocated.
echo "SLURMTMPDIR="$SLURMTMPDIR          # Displays the temporary directory path set for this job.
export I_MPI_JOB_CONTEXT=$SLURM_JOBID    # Sets MPI job context to the SLURM job ID.
echo SLURM JOB id is $SLURM_JOBID        # Prints the SLURM job ID again for logging.

export I_MPI_FABRICS=ofi:ofi             # Configures MPI to use the OpenFabrics Interface (OFI).

ulimit -s unlimited                      # Removes any limit on stack size.
ulimit -c unlimited                      # Removes any limit on core file size.
source /home/apps/SPACK/spack/share/spack/setup-env.sh  # Loads the Spack environment.
source ~/miniconda3/bin/activate         # Activates the base Conda environment.
conda activate anuga-cpu                 # Activates the 'anuga-cpu' Conda environment.

export I_MPI_DEBUG=9                     # Sets MPI debug level to verbose (9) for debugging.

spack load intel-oneapi-mpi@2021.11.0 /frfogee # Loads the Intel MPI library via Spack, version 2021.11.0.

export I_MPI_HYDRA_BOOTSTRAP=ssh         # Sets MPI bootstrapping to use SSH.

scontrol show hostname $SLURM_JOB_NODELIST | perl -ne 'chomb; print "$_" x4' | uniq -d > h.txt # Creates a list of unique node names.
sort -ru h.txt > host.txt                # Sorts and removes duplicates from 'h.txt' to create 'host.txt'.
sed -i 's/$/ slots=46/' host.txt         # Adds 'slots=46' at the end of each line in 'host.txt' for MPI task allocation.

cp host.txt hostfile1                    # Copies 'host.txt' to 'hostfile1'.
cat hostfile1 | grep hm > hm_hostfile    # Filters hostfile1 for 'hm' nodes and saves them to 'hm_hostfile'.
cat hostfile1 | grep cn > cn_hostfile    # Filters hostfile1 for 'cn' nodes and saves them to 'cn_hostfile'.

export I_MPI_HYDRA_BOOTSTRAP=ssh         # Ensures SSH is set as the MPI bootstrap method.
export UCX_LOG_LEVEL=info                # Sets UCX (Unified Communication X) logging level to 'info' for detailed logs.

(time mpiexec --hostfile hostfile1 -n $SLURM_NTASKS python run_model_3_samir.py 12-09-2021) 2>&1 | tee demo_12sep
                                        # Runs the 'run_model_3_samir.py' script on the specified date (12-09-2021) 
                                        # using mpiexec with the hostfile, and redirects output and timing info to 'demo_12sep'.

which python                             # Displays the path to the Python interpreter currently in use.

```
#### WRF

##### Introduction

WRF is a versatile, high-resolution model widely used for atmospheric research and weather forecasting. It supports both operational forecasts and scientific research applications and has flexible physics options, making it adaptable to various environments and scales.


##### Input
Dataset location – PARAM Rudra IUAC, Delhi
/home/apps/hpc_inputs/WRF

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
tar -xvf /home/apps/hpc_inputs/WRF/wrf_run.tar
ml load apps/wrf-4.5.1
cd run
time \
mpirun -np 96 wrf.exe
```
#### AMBER24

##### Introduction

Amber (Assisted Model Building with Energy Refinement) is a molecular simulation software suite used for studying the dynamics of biomolecules like proteins, DNA, RNA, and small molecules. It includes tools for system preparation, molecular dynamics simulations, and trajectory analysis, with well-validated force fields and support for both CPU and GPU computations. Amber is widely used for tasks such as energy minimization, molecular dynamics, free energy calculations, and constant pH simulations. It comprises AmberTools (a free suite for setup and analysis) and Amber MD engines (optimized for high-performance simulations on CPUs and GPUs).


<!-- 
##### Input
Dataset location – PARAM Rudra IUAC, Delhi
/home/apps/hpc_inputs/WRF -->

##### Slurm Script

```
#!/bin/bash
#SBATCH --job-name="amber"
#SBATCH --output=rfm_job.out
#SBATCH --error=rfm_job.err
#SBATCH --exclusive
#SBATCH --partition=cpu
export OMP_NUM_THREADS=1


module load apps/amber24
cd /home/apps/SourceApps/amber/amber24/AmberTools/examples/PyRESP/test/water/resp
./py_resp.run

```