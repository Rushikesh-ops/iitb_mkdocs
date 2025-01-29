# Machine Learning (ML) / Deep Learning (DL) Application Development

Most of the popular python-based ML/DL libraries are installed on the PARAM Rudra system. Users while developing and testing their applications, can use conda based python installation.

For the conda environment different modules are prepared. Users can check the list of the modules by using “module avail” command. Shown below is an example of loading conda environments in the current bash shell and continuing with application development. 

Once logged into PARAM Rudra HPC Cluster, check which all libraries are available, loaded in the current shell. To check list of modules loaded in current shell, use the command given below:
```
$ module list 
```

To check all modules available on the system, but not loaded currently, use the command given below:
```
$ module avail
```

Defaults libraries and framework specific conda environment has been made available for user to start with application development which is installed with most of the popular python packages as shown below

Loading the Conda Base Module and Activating the Environments

In order to use base conda environment we first, access and load the miniconda module, which provides access to the base environment which is installed with default packages:
```
$ module load miniconda
```

To see the list of other packages installed, use the command given below,
```
$ conda list 
```

We provide multiple conda environments that include basic machine learning packages, as well as common image processing and natural language processing packages, for your machine learning projects.

The following table shows currently available conda environments with their version (all include GPU support):


|             | Frameworks     | Environment    | Version  |
|--------------------------|----------------|----------------|----------|
|       DL Framework                    | Tensorflow     | Tensorflow     | 2.15.0   |
|                          | Tensorflow-gpu | Tensorflow-gpu | 2.15.0   |
|                          | Pytorch        | Pytorch        | 2.2.0    |
|                          | Pytorch-gpu    | Pytorch-gpu    | 2.2.1    |
|                          | Theano         | Theano         | 1.0.5    |
|                          | Theano-gpu     | Theano-gpu     | 1.0.5    |
|                          | Caffe          | Caffe          | 1.0      |
|                          | Caffe-gpu      | Caffe-gpu      | 1.0      |
|                          | Keras          | Keras          | 3.0.5    |
|                          | Keras gpu      | Keras-gpu      | 3.0.5    |
| Distributed DL Framework | Horovod        | Tensorflow     | 0.28.1   |
|                          |                | Pytorch        | 0.28.1   |
| Data science Framework   | Rapids         | Rapids         | 21.06    |

<br>
To activate any one of the environments we can load it on PARAM Rudra, load module “ENV_NAME” as shown below: 
```
$ module load <ENV_NAME>
```

Once the “ENV_NAME” module is loaded, end-users can use all libraries inside their python program.  Users can load those libraries using the “module load” command and use them for their applications. 

Example: To activate Pytorch environment we can load it on PARAM Rudra, using module load Pytorch as shown below: 
```
$ module load Pytorch
```

This will activate Pytorch environment in which users can use pytorch library and its related functionalities

Useful Conda Commands

After loading the module, you will have access to conda commands, including:
```
$ conda info --env
```
Shows available environments.
```
$ conda list -n env_name
```
Shows installed packages within an environment.
```
(env_name)$ conda deactivate
```

Deactivates an environment after loading.

For more detailed documentation, see the Conda website.

Managing and Installing Python Packages in Conda Environments
You have two options to install your own Python packages in our machine learning environment:

- Use the pip tool to install them directly
- Build your own conda environment

Consider the benefits and disadvantages of each method, before choosing which works best for you.

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> Use Conda primarily for environment management, especially in scientific computing and data science projects where non-Python dependencies are common. </p>
</div>

Use pip for installing Python packages from PyPI when you don't need the advanced environment management features provided by Conda.

##### Building Your Own Conda Environment

Building your own conda environment gives you the control to manage and install your own packages, and they will be less likely to have version errors than the pip-installed packages. The easiest way to create your own environment is to clone an existing conda environment into your own directory, then modify it.

Creating an environment can take up a significant portion of your disk quota, depending on the packages installed. To ensure that you can use your conda environment properly, please familiarize yourself with all the basic conda commands.

Conda based installation provides the latest version of DL framework, however users can install their own choice of DL framework or library version locally by following below steps.

```
Step 1. Login to Rudra cluster by using your credential.

Step 2.  Activate conda environment. 
$ module load miniconda

Step 3. Create the local environment myenv  (myenv is the environment name, you can give any name of your choice).
$ conda create --name myenv

Step 4. Activate a newly created environment. 
$ conda activate myenv

Step 5. Install your own DL framework / python library. <package-name> will get replaced by desired package which user wants to install
$ conda install <package-name>
Example: In order to install numpy we can use below command. 

$ conda install numpy
Now you can use the newly installed package in your python program.
```

##### Submitting job using sbatch script for DL Application 

You can activate your machine learning environment, run your program, and deactivate the environment in a SLURM sbatch script. For example:

```
#!/bin/bash -x
#SBATCH -N 1
#SBATCH --ntasks-per-node=<np>
#SBATCH -p cpu
#SBATCH -J <job_name>
#SBATCH -t 05:00:00
#SBATCH -o %j.out             # name of stdout output file(--output)
#SBATCH -e %j.err             # name of stderr error file(--error)
cd $SLURM_WORKDIR
module purge
module load miniconda		# load the module and environment
conda activate <env_name>	# load working environment
python <script>.py		# run python script
conda deactivate 		# deactivate environment
# end of script
```

##### How to launch a Jupyter notebook?

You can access the Jupyter notebook from your local system, while it is actually running under the conda virtual environment, setup on a remote server.  It can be accessed through the browser of your local system using ssh tunneling technique. 

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> To launch the jupyter notebook from gpu, first login to gpu node using the below command in the login node. </p>
</div>

```
$ salloc --nodes=1 --time=1:00:00 --gres=gpu:1 --partition=gpu
```

To check which gpu node is assigned, use the below command.
```
$ squeue --me
```

Now ssh to the node assigned to you. For example, in the screenshot below, you can see that gpu007 was assigned to the user.
```
$ ssh gpu007
```

Now to launch the notebook from the gpu node, follow the below steps.

- Activate the Conda environment.

To submit the job, use the below command.
```
$ module load miniconda
```
- Start the jupyter notebook by below command.
```
(base)$ jupyter notebook --ip=0.0.0.0 --port=<PORT_NO> --allow-root --no-browser
For example,
(base)$ jupyter notebook --ip=0.0.0.0 --port=8888 --allow-root --no-browser
```

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> >Token number displayed on the screen would later be used for login to jupyter notebook through your local web browser. </p>
</div>

- From another terminal, on your mobaxterm, create ssh tunneling between your local machine and remote system by executing below command
```
$ ssh -t -t username@<IP_ADDR> -L <PORT_NO>:localhost:<PORT_NO> ssh gpu<NO> -L <PORT_NO>:localhost:<PORT_NO>

For example,
$ ssh -p 4422 -t -t appsupport@<IP_ADDR> -L 8888:localhost:8888 ssh gpu007 -L 8888:localhost:8888
```

<div class="admonition note"> <p class=admonition-title>Note</p>
<p> Use the port number and gpu node that is assigned by slurm. </p>
</div>

- Type the below address in your local browser to access Jupyter notebook.

```
https://localhost:<PORT_NO>

For example,
https://localhost:8888
Note: Enter token number for login
```

-  The Jupyter notebook can now be opened after entering the valid token
