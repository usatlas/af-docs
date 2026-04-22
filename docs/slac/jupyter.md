# [The SLAC JupyterLab](https://sdf.slac.stanford.edu/public/doc/#/interactive-compute?id=jupyter)

## Login to JupyterLab at SLAC

Please refer to ["S3DF: a new ..."](accessing.md#accessing-to-s3df) for
information about logging in to
[SLAC Jupyter Web portal](https://s3df.slac.stanford.edu/ondemand), available
spaces and network constrains.

## How to launch JupyterLab at SLAC

Once you login to the main portal, click "My Interactive Sessions" from the top
menu bar. Then choose "Jupyter". You will need to make a few choices:

1. In "Jupyter Instance" box, choose "atlas/20210403". You can choose Jupyter
   Instances for other experiments but there is no guarantee that those
   instances will work for you. You can also bring your own Jupyter environment
   (under Custom)
2. Check the "Use JupyterLab instead of Jupyter Notebook?" box.
3. In the account box, type "atlas:usatlas".
4. In the "Partition" box, choose "rome" for CPU only session, or "ampere" if
   you need GPUs
5. Choose hours, # CPUs, memory, # GPUs and GPU type, then click "launch". Note
   that your Jupyter work runs as a SLURM job. So choose only what you need to
   ensure speedy launching of your job.

## Run your own Jupyter environment

The ATLAS instance we built may not satisfy your need. If you have your own
Jupyter environment that is accessible from SLAC (on SLAC disk or in CVMFS), you
may be able to run it on SLAC's Jupyter infrastructure. To do so:

1. Following the same steps above to launch Jupyter at SLAC.
2. Instead of choosing the "atlas-jupyter..." instance, you choose "Custom
   Singularity Image" or
   "[Custom Conda Environment](https://docs.conda.io/projects/conda/en/latest/user-guide/install/linux.html)".
3. The "Commands to initiate Jupyter" box will be pre-filled with commands to
   prepare your instance. You can edit/paste whatever Shell script to the box to
   prepare launching your Jupyter environment.
4. A backend launching script will "source" your Shell script. It expects that
   after "sourcing", there is a command (or Shell function) called "jupyter" for
   it to run. It will then run one of the following commands, `jupyter notebook`
   or `jupyter lab` (depend on whether you choose Jupyter Notebook or Jupyter
   Lab) to launch your Jupyter environment.
5. Below are example scripts to prepare for launching Jupyter from a Singularity
   container:

```
export SINGULARITY_IMAGE=/sdf/data/atlas/u/$(id -un)/.../my_singularity_image.sif
function jupyter() { singularity exec --nv -B /sdf,/cvmfs ${SINGULARITY_IMAGE} jupyter $@ }
```

or from a Conda environment (assuming Anaconda 3 is installed at ~/anaconda3,
and `jupyterlab` is installed via Conda):

```
source ~/anaconda3/etc/profile.d/conda.sh
conda activate
```

## Kernels and extensions in the ATLAS Jupyter environment

The Jupyter environment provides several kernels and extensions. This includes:

1. python2 with pyroot and uproot. By default, **AnalysisBase,21.2.111** is
   loaded before the pyroot2 kernel is launched. To overwrite this, create a
   file [$HOME/notebooks/.user_setups](SLACuser_setups.txt) in your home
   directory (even if your home directory is in AFS)
2. ROOT C++. The ATLAS environment is set before the kernel is launched. The
   overwrite method is the same as the above.
3. python3 with pyroot, uproot3/awkward and Dask.
4. python3 with pyroot, uproot3/awkward and [RAPIDS.AI](https://rapids.ai)
   packages (cuPy, cuDF, cuML, Dask/Dask-CUDA, etc.). Choose "# of GPUs" (at
   least 1) and "GPU type" before launching Jupyter.
5. python3 with pyroot, uproot3/awkward and TensorFlow(GPU) and Keras. Choose "#
   of GPUs" (at least 1) and "GPU type" before launching Jupyter.
6. Terminal console for simple interactive use, e.g. file managements. It also
   include python2.7/python3, gcc/g++, gdb, make, cmake3, xrootd-clients,
   openssh-client, curl, vi, SLURM clients, etc.
7. Markdown document editor and previewer. You can edit and preview in two tabs
   simultaneously.

## Extend ATLAS JupyterLab Functionalities

Python's pip module allows users to add packages to the JupyterLab environment
as they need. For example, one can use PYCUDA and DASK distributed scheduling
with SLURM. Check out [this doc](jupyter_extra.md) on how to do that.

## Getting help

!!! note "Need help?"

    See our [Getting Help](../getting_help.md) page for support options and how to
    reach the ATLAS AF team.
