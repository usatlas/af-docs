# [The BNL JupyterLab](https://atlas-jupyter.sdcc.bnl.gov)

Before accessing the BNL JupyterHub - <https://atlas-jupyter.sdcc.bnl.gov/>,
please apply for a BNL computing account.
[Link to the instructions.](account.md). BNL's Scientific Data Computing Center
(SDCC) provides JupyterHub environment on their HTC (high throughput computing)
cluster and HPC (high performance computing) clusters. Choose one of them to
login.

## The JupyterHub resources on HTC cluster and HPC clusters

Jupyter instances running under the HTC JupyterHub are actually Condor batch
jobs. Using HTC JupyterHub requires affiliation to an experiment, such as ATLAS.

Jupyter instances running under the HPC JupyterHub are run as SLURM batch jobs
on either
[BNL Institutional Cluster (IC)](https://www.racf.bnl.gov/experiments/sdcc/institutional-cluster/information)
or
[KNL cluster](https://www.racf.bnl.gov/experiments/sdcc/knl-cluster/information).
Using HPC JupyterHub requires a valid time allocation on those clusters. Nvidia
GPUs are available on the IC cluster and Intel Xeon Phi Knights Landing (KNL)
CPU are available on the KNL cluster.

## Kernels and extensions in the ATLAS Jupyter environment

The Jupyter environment provides several kernels and extensions. This includes:

1. python2 with pyroot and uproot. By default, **AnalysisBase,21.2.111** is
   loaded before the pyroot2 kernel is launched. To overwrite this, create a
   file `$HOME/notebooks/.user_setups` in your home directory (even if your home
   directory is in AFS)
2. ROOT C++. The ATLAS environment is set before the kernel is launched. The
   overwrite method is the same as the above.
3. python3 with pyroot and uproot.
4. python3 kernels with Tensorflow on CPU, Tensorflow on GPU and ML packages.
5. Terminal console for simple interactive use
6. Markdown document editor and previewer. You can edit and preview in two tabs
   simultaneously.

## Getting help

!!! note "Need help?"

    See our [Getting Help](../getting_help.md) page for support options and how to
    reach the ATLAS AF team.
