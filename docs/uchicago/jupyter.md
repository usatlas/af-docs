# The UChicago JupyterLab

To support machine learning code development, our users can deploy one or more
private JupyterLab applications.

To encourage fair sharing these applications are time limited. We also ask users
to request only the resources that they need.

## How to launch JupyterLab at UChicago

Once you login, click "Services" on the top menu bar, then choose "JupyterLab".
You will need to make some choices in order to configure your JupyterLab
notebook:

1. Provide a notebook name that has no whitespace, using 30 characters or less
   from the set [a-zA-Z0-9._-] to name your notebook.
2. You can request 1 to 16 CPU cores.
3. You can request 1 to 32 GB of memory.
4. You can request 0 to 7 GPU instances.
5. A notebook can have lifetime of up to 72 hours (1 to 168 hours).
6. You can select a GPU model based on its memory size. If you request a GPU,
   please make sure the GPU is available, by clicking on the icon next to GPU
   memory.
7. You can choose a Docker image from the dropdown.

## Resource Limitations

- You can request 1 to 16 CPU cores.
- You can request 1 to 32 GB of memory.
- You can request 0 to 7 GPU instances.
- A notebook can have lifetime of up to 72 hours.
- You can select a GPU model based on its memory size. If you request a GPU,
  please make sure the GPU is available, by clicking on the icon next to GPU
  memory.

## Selecting GPU memory and instances

The AF cluster has four NVIDIA A100 GPUs. Each GPU can be partitioned into seven
GPU instances. This means the AF cluster can have up to 28 GPU instances running
in parallel.

A user can request 0 to 7 GPU instances as a resource for the notebook. A user
can request 40,836 MB of memory for an entire A100 GPU, or 4864 MB of memory for
a MIG instance.

## Docker Images

### ml_platform

The primary image available is `ml_platform`, a comprehensive machine learning
platform that includes:

- **Python 3.12** with ML frameworks (TensorFlow, Keras, scikit-learn)
- **ROOT 6.32+** for HEP analysis
- **JupyterLab** with extensions (RISE, Git integration, ipywidgets)
- **HEP tools** (uproot, atlasify, rucio-jupyterlab)
- **NVIDIA GPU support** (CUDA 13.0)
- Data science libraries (NumPy, Pandas, SciPy, PyArrow, HDF5)

**Available tags:**

- `ml_platform:latest` - Latest stable version (recommended)
- `ml_platform:YYYY.MM` - Specific (older) release versions

For the complete list of packages, version information, and detailed
documentation, see the
[ml_platform repository](https://github.com/maniaclab/ml_platform).

#### Installing

You can install additional packages directly from your notebook with
[`pixi`](https://pixi.prefix.dev/latest/). The `ml_platform` image organizes
packages under features. For ML-related packages, use the `ml` feature (`-f ml`)
and install them with the `ml` environment (`-e ml`).

**Example**: installing the GPU-version of `pytorch` along with `torchvision`
and `xgboost` available on [conda-forge](https://conda-forge.org/packages/), you
can run the following inside the notebook

```
pixi add -f ml pytorch-gpu torchvision xgboost
pixi install -e ml
```

### AnalysisBase Images

- **AB-stable** - Based on AnalysisBase
- **AB-dev** - Based on AnalysisBase but with cutting edge uproot, dask, awkward
  arrays, etc.

/// note | Other Images

Additional images may be available in the dropdown menu. Contact the facility
team if you need a specific software environment not provided by these images.

///

## Getting help

For software additions, upgrades, or questions about the JupyterLab environment:

- See our [Getting Help](../getting_help.md) page for support options
- [Contact the UChicago facility team](../getting_help.md#facility-specific-support)
- [Open an issue](https://github.com/maniaclab/ml_platform/issues) for
  ml_platform image problems
