# The UChicago BinderHub

[BinderHub](https://binderhub.af.uchicago.edu/) is the recommended way to run
Jupyter notebooks at the UChicago Analysis Facility. It launches a JupyterLab
server on the AF from either a curated, pre-built image or any Git repository
you point it at.

!!! warning "Replacing the AF Portal JupyterLab"

    The JupyterLab service in the AF Portal (**Services** → **JupyterLab**) is
    deprecated and will be removed on December 1st. Please move your work to
    BinderHub. See [The UChicago JupyterLab](jupyter.md) for the deprecated
    service.

## How to launch a notebook

1. Go to
   [https://binderhub.af.uchicago.edu/](https://binderhub.af.uchicago.edu/) (or
   choose **Services** → **BinderHub** in the
   [AF Portal](https://af.uchicago.edu/)) and log in with your AF account.
2. Optionally open **Advanced setting - sites and resource customizations** to
   request GPUs or change the CPU and memory for your notebook (see
   [Resources](#resources-advanced-setting) below).
3. Under **Curated Repositories**, choose an image from the **Select a curated
   repo...** dropdown. BinderHub fills in the repository and Git ref for you and
   starts launching.
4. Wait for the progress bar to finish. You can follow along in **Build Logs**.
   Once the server is ready, JupyterLab opens in your browser.

Your running servers are listed on the JupyterHub home page at
[https://jupyterhub.af.uchicago.edu/hub/home](https://jupyterhub.af.uchicago.edu/hub/home),
where you can reopen or stop them. The **JupyterHub Home** button in the
advanced settings panel also takes you there.

## Curated repositories

Curated repositories are pre-built images maintained by the facility team. They
start quickly because they don't need to be built, so we expect most users to
pick one of these:

| Curated repo         | Source                                                                                               | Description                                                |
| -------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `AB-STABLE`          | [`usatlas/analysisbase-dask-uc`](https://github.com/usatlas/analysisbase-dask-uc) (`binderhub-prod`) | Based on AnalysisBase                                      |
| `AB-DEV`             | [`usatlas/analysisbase-dask-uc`](https://github.com/usatlas/analysisbase-dask-uc) (`binderhub-dev`)  | AnalysisBase with cutting edge uproot, dask, awkward, etc. |
| `ml-platform:latest` | [`maniaclab/ml_platform`](https://github.com/maniaclab/ml_platform) (`main`)                         | Latest `ml_platform` machine learning image (recommended)  |
| `ml-platform:2026.5` | [`maniaclab/ml_platform`](https://github.com/maniaclab/ml_platform) (`v2026.5.15`)                   | Pinned `ml_platform` release                               |

These are the same images offered by the AF Portal JupyterLab. See
[Docker Images](jupyter.md#docker-images) for what each one contains and how to
install additional packages with `pixi`.

## Resources (advanced setting)

Click **show** next to **Advanced setting - sites and resource customizations**
to customize your notebook's resources before launching:

- **Sites**: the cluster your notebook runs on. Keep the default, **UC AF**,
  unless you have a reason to use another site.
- **GPU Model - name (avail/total)**: the GPU model to request. Each entry shows
  how many GPUs of that model are free out of the total, for example
  `NVIDIA-GeForce-RTX-2080-Ti (21/24)`. Models with none free are greyed out.
  Choose **Any (auto-assign GPU)** if you don't need a specific model.
- **GPU count** (the number box next to the GPU model): how many GPUs to
  request. Leave it at `0` for a CPU-only notebook.
- **QoS — Guaranteed**: check this box to reserve a fixed amount of CPU and
  memory for your notebook. This enables the **CPU** and **Memory** fields.
- **CPU**: the number of CPU cores to reserve (1 to 32).
- **Memory**: the amount of memory to reserve, in GB.

The GPU list and availability depend on the selected site, and models like
`NVIDIA-A100-SXM4-40GB-MIG-1g.5gb` are slices (MIG instances) of a larger GPU.
Please request only the resources you need, especially GPUs, which are shared by
everyone on the facility.

## Launching your own repository

You can also build and launch any Git repository, for example a fork of one of
the curated repos with your own dependencies:

1. In **Build and launch a repository**, pick the repository type (GitHub,
   GitLab, Git repository, Zenodo DOI, ...) and enter the repository name or
   URL.
2. Set the **Git ref** (branch, tag, or commit). It defaults to `HEAD`.
3. Optionally give a **File to open** once JupyterLab starts.
4. Click **launch**.

BinderHub builds a Docker image from the dependency files in your repository
(such as `requirements.txt` or `environment.yml`), so the first launch of a new
commit takes longer. See the
[Binder documentation](https://mybinder.readthedocs.io/en/latest/using.html#preparing-a-repository-for-binder)
for how to prepare a repository. The form also shows a reusable launch URL, like
`https://binderhub.af.uchicago.edu/v2/gh/<org>/<repo>/<ref>`, and a badge you
can add to your README.

## Your files and storage

Your notebook runs as your AF user and starts in your AF home directory,
`/home/<username>`. Your `/data` directory and `/cvmfs` are also available.
Files saved there persist after your notebook stops; anything saved elsewhere in
the container is lost.

Idle notebooks are stopped automatically after 24 hours.

## Connecting an MCP client

BinderHub notebooks support attaching an MCP client such as Claude Code. See
[Connecting an MCP client to your notebook](jupyter.md#connecting-an-mcp-client-to-your-notebook)
and follow the steps for BinderHub-launched notebooks.
