# Preparing Your Own Executable

The compilations are done on the login node, whereas the execution happens on the compute nodes via the scheduler (SLURM).

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> The compilation and execution must be done with the same libraries and matching version to avoid unexpected results. </p>
</div>

Steps:

- Load required modules on the login node.
- Do the compilation.
- Open the job submission script and specify the same modules to be loaded as used while compilation.
- Submit the script.

The directory contains a few sample programs and their sample job submission scripts. The compilation and execution instructions are described in the beginning of the respective files.

The user can copy the directory to his/her home directory and further try compiling and executing these sample codes. The command for copying is as follows:
```
cp -r /home/apps/Docs/samples/ ~/.
```

- mm.c          	- Serial Version of Matrix-Matrix Multiplication of two NxN matrices
- mm_omp.c     	- Basic OpenMP Version of Matrix-Matrix Multiplication of two NxN matrices
- mm_mpi.c     	- Basic MPI Version of Matrix-Matrix Multiplication of two NxN matrices
- mm_acc.c 	- OpenAcc Version of Matrix-Matrix Multiplication of two NxN matrices
- mm_blas.cu   	 - CUDA Matrix Multiplication program using the cuBlas library.
- mm_mkl.c     	 - MKL Matrix Multiplication program.
- laplace_acc.c 	- OpenACC version of the basic stencil problem.

It is recommended to use the intel compilers since they are better optimized for the hardware.

**Compilers**

| Compilers               | Description                                  | Versions Available                                                                |
|-------------------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| gcc/gfortran            | GNU Compiler (C/C++/Fortran)                 | 8.5.0, 9.3.0, 12.2.0, 13.2.0                                                      |
| icc/icpc/ifort          | Intel Compilers (C/C++/Fortran)              | 2021.5.0, 2021.11.0, 2021.10.0  oneapi@2024.0.0, oneapi@2023.2.0, oneapi@2022.0.0 |                                                                                  |
| mpicc/mpicxx/mpif90     | Intel-MPIwith GNU compilers (C/C++/Fortran)  | 2021.11.0                                                                         |
| mpiicc/mpiicpc/mpiifort | Intel-MPIwithIntel compilers (C/C++/Fortran) | 2021.11.0                                                                         |
| nvcc                    | CUDA C Compiler                              | 9.1.85, 11.8.0, 12.3.0                                                            |

**Optimization Flags**

Optimization flags are meant for uniprocessor optimization, wherein, the compiler tries to optimize the program, on the basis of the level of optimization. The optimization flags may also change the precision of output produced from the executable. The optimization flags can be explored more on the respective compiler pages. A few examples are given below.
```
Intel:  -O3 –xHost
GNU: -O3
PGI: -fast
```

Given next is a brief description of compilation and execution of the various types of programs. However, for certain bigger applications, loading of additional dependency libraries might be required.

<p style="color:#259AA4;text-decoration:underline">C Program:</p>

```
Setting up of environment: 
spack load gcc@13.2.0 /3wdooxp
spack load intel-oneapi-compilers@2024.0.0
compilation: icc -O3 -xHost <<prog_name.c>>
Execution: ./a.out
```

<p style="color:#259AA4;text-decoration:underline">C + OpenMP Program:</p>

```
Setting up of environment:  
spack load gcc@13.2.0 /3wdooxp
spack load intel-oneapi-compilers@2024.0.0 
Compilation: icc -O3 -xHost -qopenmp <<prog_name.c>>
Execution: ./a.out
```

<p style="color:#259AA4;text-decoration:underline">C + MPI Program:</p>

```
Setting up of environment:  
spack load gcc@13.2.0 /3wdooxp
spack load intel-oneapi-compilers@2024.0.0
Compilation: mpiicc -O3 -xHost <<prog_name.c>>
Execution: mpirun -n <<num_procs>> ./a.out
```
<p style="color:#259AA4;text-decoration:underline">C + MKL Program:</p>

```
Setting up of environment:
spack load gcc@13.2.0 /3wdooxp
spack load intel-oneapi-compilers@2024.0.0
Compilation: icc -O3 -xHost -mkl <<prog_name.c>>
Execution: ./a.out
```

<p style="color:#259AA4;text-decoration:underline">CUDA Program:</p>

```
Setting up of environment: 
spack load gcc@12.2.0 /h2acmw7
spack load cuda@12 /qpi4kl6

Example (1)
Compilation: nvcc -arch=sm_70<<prog_name.cu>>
Execution: ./a.out 
Note: The optimization switch -arch=sm_70 is intended for Volta V100 GPUs and is valid for CUDA 9 and later. Similarly, older versions of CUDA have compatibility with lower versions of GCC only. Accordingly, appropriate modules of GCC must be loaded. 


Example (2)
Compilation: nvcc -arch=sm_70 /home/apps/Docs/samples/mm_blas.cu -lcublas
Execution: ./a.out
```

<p style="color:#259AA4;text-decoration:underline">CUDA OpenMP Program:</p>

```
Setting up of environment: 
spack load gcc@12.2.0 /h2acmw7
spack load cuda@12 /qpi4kl6

Example (1)
Compilation: nvcc -arch=sm_70 -Xcompiler="-fopenmp" -lgomp /home/apps/Docs/samples/mm_blas_omp.cu -lcublas
Execution: ./a.out 


Example (2)
Compilation: g++ -fopenmp /home/apps/Docs/samples/mm_blas_omp.c -I//home/apps/SPACK/spack/opt/spack/linux-almalinux8-cascadelake/gcc-12.2.0/cuda-12.3.0-qpi4kl6p2yuvmsvkf4daevk7zcr4tu27/include  -L/home/apps/SPACK/spack/opt/spack/linux-almalinux8-cascadelake/gcc-12.2.0/cuda-12.3.0-qpi4kl6p2yuvmsvkf4daevk7zcr4tu27/lib64 -lcublas
Execution: ./a.out
```

<p style="color:#259AA4;text-decoration:underline">OpenACC Program:</p>

```
Setting up of environment: 
spack load pgi@19.10 cuda@10.1
 
Compilation for GPU: pgcc -acc -fast -Minfo=all -ta=tesla:cc70,managed/home/apps/Docs/samples/laplace_acc.c
Execution:./a.out

Compilation for CPU: pgcc -acc -fast -Minfo=all -ta=multicore-tp=skylake /home/apps/Docs/samples/laplace_acc.c
Execution:./a.out.
```