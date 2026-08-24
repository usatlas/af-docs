# Jupyter At US ATLAS AFs

One of the services available at the US ATLAS analysis facilities is the Jupyter
Lab environment. Some of the Jupyter kernels available there includes:

1. python2 with pyroot and uproot, some allows users to load an ATLAS analysis
   releases in order to access xAODs.
2. python3 with pyroot and uproot.
3. python3 with ML packages such as Tensorflow and Keras, with support of Nvidia
   GPUs.
4. Terminal console for simple interactive use, e.g. file managements.

Use the following links to access the Jupyter environment at the AFs, or access
site specific documents on Jupyter.

- BNL JupyterLab - <https://jupyter-fedid.sdcc.bnl.gov/> and
  [documents](../bnl/jupyter.md).
- [SLAC JupyterLab](https://sdf.slac.stanford.edu/public/doc/#/interactive-compute?id=jupyter)
  and [documents](../slac/jupyter.md).
    - SLAC is migrating to a new computing facility SDF. As a result, we
      recently made some change to the SLAC JupyterLab portal. If you haven't
      read and prepare yourself for "SLAC ID", please refer to
      ["SSH login to SDF"](../slac/accessing.md#ssh-login-to-sdf) to estiblish
      your "SLAC ID", then `ssh sdf-login.slac.stanford.edu` for the first time.
    - The SLAC JupyterLab link above will change in the near future as the
      migration continue. When that happens, we will update the above link.
    - When asked to choose an identity provider, please choose "SLAC National
      Accelerator Laboratory" and then use your
      ["SLAC ID"](../slac/accessing.md#ssh-login-to-sdf) to login.

# Examples of using Jupyter to analyze xAODs

- [How to get information about an xAOD in python](examples/xAODcheck.md)
- [Read xAODs using PyROOT](https://github.com/usatlas/docs/blob/master/jupyter/examples/pyROOT_example.ipynb)
- [Info about uproot and xAOD](examples/convert_specific_variables.py.txt)
