# Extend functionalities

Though pip, you can add more packages to your python/Jupyter. The following are
examples that apply to python3

## Use DASK with SLURM

[DASK](https://docs.dask.org/en/latest/) allows you to break down a large
calculation into smaller parallel pieces. Here we are mostly interested in using
DASK to spread the calculation via multiple SLURM jobs.

To enable DASK distributed scheduling/running with SLURM, first make sure when
you start a JupyterLab instance, you choose an image that supports SLURM job
submission, such as `atlas/20210403`. You will also need to use pip3 to install
`dask-jobqueue` and `distributed`:

Open a Terminal in JupyterLab and run the following command:

`python3 -m pip install --ignore-installed dask numpy dask-jobqueue distributed --user`

To test whether it works, try [this python script](dask.slurm.test.py.txt) in
JupyterLab. Please pay attention to the line `python="/usr/bin/python3"` in the
script - do not forget about it.

### Access DASK Dashboard

For those who want to use
[DASK dashboard](https://docs.dask.org/en/latest/diagnostics-distributed.html),
there is a few things to prepare:

1. Use pip3 to install `bokeh` (in order to start DASK dashboard automatically).
   This is a one time task.
2. Optional: Adjust the DASK dashboard port number. By default, the dashboard
   listens to port 8787. You may need to change this port number if it is used
   by other users or by your other DASK programs. In the above example, line
   `scheduler_options={'dashboard_address': ':8787'}` specify the port number.
    - You don't have to do this: if you remove this `scheduler_options= ...`
      line, DASK will select an available port for you. In that case, you can
      print the `client` object in find out the host:port.
3. Port forwarding
    - Open a terminal in Jupyter Lab and find out the host name (use command
      'hostname'). Let's say you are on host `rome0100`.
    - From your desktop, `ssh -L 8787:rome0100:8787 sdf-login.slac.stanford.edu`
      .
    - In your web browser, open `http://localhost:8787/status` to access the
      DASK dashboard.

## Use your own Conda environment

You can use your own
[Conda environment](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html#)
along with the ATLAS Jupyter environment. Suppose you want to setup
[pyhf](https://github.com/scikit-hep/pyhf) via Conda, and use it in ATLAS
Jupyter environment, here is what you can do:

Open a Terminal in JupyterLab, you have two choices:
"`python3 -m pip install pyhf`" is the easiest way (but not via Conda). We want
to show how to do this in an Conda environment, and make it available in
Jupyter.

Suppose you already have miniconda3 or Anaconda3 install:

1. Create a new Conda environment, and name it "mypyhf":
   `conda create --name mypyhf`
2. Activate the environment: `conda activate mypyhf`
3. Install "ipykernel" and "pyhf" in this Conda environment:
   `conda install ipykernel pyhf`
4. Make this 'mypyhf' environment available in Jupyter:
   `python3 -m ipykernel install --user --name=mypyhf`
5. (To reverse, do `jupyter kernelspec uninstall mypyhf`)

After this and restart the Jupyter environment, you will see a new kernel call
`mypyhf`

## Using built-in Conda environments

Some images, such as `atlas/20210403` has Andconda3 built-in to support Conda
environments such as ["rapids-0.18"](https://rapids.ai) and
"tf-keras-gpu". You can use these Conda environments by
`source /opt/anaconda3/etc/profile.d/conda.sh`. Alternatively, you can also mix
them with your own Conda installation and environments by creating a file
`~/.condarc` and put the following lines inside

```
envs_dirs:
  - ~/anaconda3/envs
  - /opt/anaconda3/envs
```

Both "rapids-0.18" and "tf-keras-gpu" support uproot3 and pyroot (ROOT 6.22.08)

### What is "rapids-0.18"

See https://rapids.ai for detail. In short, packages such as `cupy`, `cudf`
provide `numpy` and `pandas` equivalent but run in Nvidia GPUs.

## Install PYCUDA

Open a Terminal in JupyterLab and run the following command:

`python3 -m pip install pycuda --user`

To test whether your PYCUDA works, try [this python script](pycuda.test.py.txt)
in JupyterLab. The script contains two URLs that explain the concepts of CUDA
thread blocks and thread indexing.
