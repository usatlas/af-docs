# The UChicago BinderHub

[BinderHub](https://binderhub.af.uchicago.edu/) is the recommended way to run
Jupyter notebooks at the UChicago Analysis Facility. It launches a JupyterLab
server on the AF from either a curated, pre-built image or any Git repository
you point it at.

!!! warning "Replacing the AF Portal JupyterLab"

    The JupyterLab service in the AF Portal (**Services** → **JupyterLab**) is
    no longer available. Please move your work to BinderHub. See
    [The UChicago JupyterLab](jupyter.md) for the retired service.

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

| Curated repo                | Source                                                                                                   | Description                                                |
| --------------------------- | -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| `AB-STABLE`                 | [`usatlas/analysisbase-dask-uc`](https://github.com/usatlas/analysisbase-dask-uc) (`binderhub-prod`)     | Based on AnalysisBase                                      |
| `AB-DEV`                    | [`usatlas/analysisbase-dask-uc`](https://github.com/usatlas/analysisbase-dask-uc) (`binderhub-dev`)      | AnalysisBase with cutting edge uproot, dask, awkward, etc. |
| `ml-platform:latest`        | [`maniaclab/ml_platform`](https://github.com/maniaclab/ml_platform) (`main`)                             | Latest `ml_platform` machine learning image (recommended)  |
| `ml-platform-cpu:latest`    | [`maniaclab/ml_platform-cpu-binder`](https://github.com/maniaclab/ml_platform-cpu-binder) (`main`)       | Latest CPU-only `ml_platform` image                        |
| `ml-platform-gpu:latest`    | [`maniaclab/ml_platform-gpu-binder`](https://github.com/maniaclab/ml_platform-gpu-binder) (`main`)       | Latest GPU `ml_platform` image                             |
| `ml-platform-cpu:2026.11.4` | [`maniaclab/ml_platform-cpu-binder`](https://github.com/maniaclab/ml_platform-cpu-binder) (`v2026.11.4`) | Pinned CPU-only `ml_platform` release                      |
| `ml-platform-gpu:2026.11.4` | [`maniaclab/ml_platform-gpu-binder`](https://github.com/maniaclab/ml_platform-gpu-binder) (`v2026.11.4`) | Pinned GPU `ml_platform` release                           |

These are the same images previously offered by the AF Portal JupyterLab. See
[Docker Images](#docker-images) for what each one contains and how to install
additional packages with `pixi`.

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

- `ml-platform-cpu:latest` - Latest stable version (recommended)
- `ml-platform-cpu:<release>` - Pinned release, e.g. `ml-platform-cpu:2026.11.4`
- `ml-platform-gpu:latest` - Latest stable version (recommended)
- `ml-platform-gpu:<release>` - Pinned release, e.g. `ml-platform-gpu:2026.11.4`

For the complete list of packages, version information, and detailed
documentation, see the
[ml_platform repository](https://github.com/maniaclab/ml_platform).

#### Installing Additional Packages

You can install additional packages directly from your notebook with
[`pixi`](https://pixi.prefix.dev/latest/). The `ml_platform` image organizes
packages under features. ML-related packages live in the `mlbase` feature
(`-f mlbase` / `--feature mlbase`), which is shared by the `ml` and `mlcpu`
environments. If you are not using a GPU-node, you can use the `mlcpu`
environment which has the same set of packages without the `cuda` system
requirement.

**Example**: installing the GPU-version of `pytorch` along with `torchvision`
and `xgboost` available on [conda-forge](https://conda-forge.org/packages/), you
can run the following inside the notebook

```bash
!pixi add pytorch-gpu torchvision xgboost --feature mlbase
!pixi lock
```

Then restart the kernel. The `!` prefix runs the command directly from a
notebook cell; you can also open a terminal in JupyterLab and run the same
commands without the `!`.

For more advanced pixi usage, including custom environments and
multi-environment support, see the [pixi](#pixi) section below.

### AnalysisBase Images

- **AB-stable** - Based on AnalysisBase
- **AB-dev** - Based on AnalysisBase but with cutting edge uproot, dask, awkward
  arrays, etc.

!!! note "Other Images"

    Additional images may be available in the **Select a curated repo...**
    dropdown. Contact the facility
    team if you need a specific software environment not provided by these images.

## Resources (advanced setting)

Click **show** next to **Advanced setting - sites and resource customizations**
to customize your notebook's resources before launching:

- **Sites**: choose which cluster your notebook runs on. The default is **UC
  AF**; you can also choose **ssl** or **nrp** (the
  [National Research Platform](https://nrp.ai/)), for example if the GPU you
  need is not available on the UC AF.
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

## pixi

[`pixi`](https://pixi.prefix.dev/latest/) is a modern package manager that
provides powerful environment management capabilities. While the `ml_platform`
image includes basic pixi support for adding packages, pixi can also create
fully isolated custom environments for different projects using
[`pixi-kernel`](https://github.com/renan-r-santos/pixi-kernel).

### Creating Custom Environments with pixi-kernel

For complex setups where you need isolated environments for different projects,
you can use pixi-kernel to create and manage multiple custom kernels in
JupyterLab.

**Understanding pixi features and environments:**

- A **feature** is a named collection of packages (e.g., `data-science`, `nlp`)
- An **environment** can combine multiple features together
- Multiple environments can point to the same features in different combinations
- This gives you full control over your dependencies, even more than conda/pip

Learn more about pixi's multi-environment support in the
[official documentation](https://pixi.prefix.dev/latest/workspace/multi_environment/).

**Creating a custom environment from a conda YAML file:**

If you have an existing conda `environment.yml` file, you can import it into
pixi and register it as a kernel:

1. Create your environment file (e.g., `my_env.yml`):

    ```yaml
    name: my-custom-env
    channels: ["conda-forge"]
    dependencies:
        - mplhep
        - scikit-learn
        - pytorch-cpu # or pytorch-gpu
        - torchinfo
        - pytorch_geometric
        - pip:
              - da4ml
              - atlas-mpl-style
    ```

2. Import the environment into pixi as a new feature:

    ```bash
    pixi import --format=conda-env -f my-custom-env my_env.yml
    ```

3. Register the kernel with pixi-kernel:

    ```bash
    pixi add -f my-custom-env pixi-kernel
    ```

**Using your custom kernel in JupyterLab:**

1. When you first start JupyterLab, you'll be using the default `ipykernel`
2. To switch to your custom environment:
    - Click the kernel selector in the top-right corner
    - Select the `pixi` kernel (this enables multi-environment support)
    - Click the property inspector (gear icon) on the right sidebar
    - Select your custom environment (e.g., `my-custom-env`)
    - Save the notebook
    - Restart the kernel

Your notebook will now use the selected pixi environment. Each notebook can use
a different environment, and the selection is saved with the notebook.

**Additional resources:**

- [Pixi import tutorial](https://pixi.prefix.dev/latest/tutorials/import/) -
  various ways to import existing environments
- [Pixi multi-environment guide](https://pixi.prefix.dev/latest/tutorials/multi_environment/) -
  comprehensive tutorial on managing multiple environments
- [pixi-kernel GitHub](https://github.com/renan-r-santos/pixi-kernel) - kernel
  integration documentation

### Creating Features from Scratch

Instead of importing from a conda YAML file, you can create features directly
with pixi commands. This is useful when you want to build an environment
incrementally or don't have an existing environment file.

**Example: Creating a custom analysis feature:**

```bash
# Create a new feature with core packages, including pixi-kernel
!pixi add -f analysis numpy pandas matplotlib scipy uproot awkward hist pixi-kernel

# Specify Python version if needed
!pixi add -f analysis "python>=3.11"
```

Creating a feature does not automatically create a matching environment or
kernel — you must register one explicitly, combining as many features as you
need:

```bash
!pixi project environment add debug -f mlbase -f analysis
!pixi lock
```

You can also combine packages from different channels:

```bash
# Add packages from specific channels
pixi add -f ml-custom -c pytorch -c conda-forge pytorch torchvision
pixi add -f ml-custom scikit-learn xgboost
pixi add -f ml-custom pixi-kernel
pixi project environment add ml-custom -f ml-custom
pixi lock
```

Once created, restart the kernel so the new environment (e.g. `debug`) shows up
in the property inspector, then follow the same steps described in "Using your
custom kernel in JupyterLab" above to select it, save the notebook, and restart
the kernel again. The first time you switch to a newly created environment, pixi
has to build it from scratch, which can hang for up to a minute while it
installs all the packages — this is expected and only happens once.

### Managing Environments

As you work with pixi, you'll need to manage your features and environments.
Here are essential commands for day-to-day operations:

**View available features and environments:**

```bash
# Show all features, environments, and installed packages
pixi info

# List packages in a specific environment
pixi list -e my-env
```

**Update packages:**

```bash
# Update specific packages in a feature
pixi update -f mlbase pytorch torchvision

# Update all packages in a feature
pixi update -f mlbase

# Update all packages in all environments
pixi update
```

**Remove packages:**

```bash
# Remove a package from a feature
pixi remove -f analysis matplotlib

# Remove multiple packages
pixi remove -f analysis matplotlib seaborn plotly
```

**Clean up disk space:**

```bash
# Remove unused packages and caches
pixi clean

# Remove a specific environment's cache
pixi clean cache -e my-env
```

**Reinstall an environment:**

If an environment becomes corrupted or you want to start fresh:

```bash
# Remove and reinstall
pixi install -e my-env
```

### Best Practices

Choose the right approach for your needs to keep your environments manageable
and maintainable:

**When to use the simple approach** (add to the `mlbase` feature):

- You need just a few additional packages
- Packages are compatible with the existing `ml` or `mlcpu` environment
- You're doing exploratory work or quick prototyping
- You don't need strict version control

**When to create a custom environment:**

- You need specific package versions that conflict with `ml` or `mlcpu`
- You're working on a long-term project with specific dependencies
- You want to isolate different projects from each other
- You're collaborating and need reproducible environments
- You need different Python versions for different projects

**Preserving environments across sessions:**

JupyterLab instances run in ephemeral Docker containers. When your notebook
server stops, custom pixi environments are **not preserved**. To recreate your
environments in a new session:

- Save your environment YAML files (e.g., `my_env.yml`) in your persistent home
  directory or a git repository
- When starting a new JupyterLab session, re-run the `pixi import` and
  `pixi add` commands to recreate your environments
- Consider creating a setup script in your home directory that recreates all
  your environments automatically

**Environment naming:**

Use descriptive names that indicate the purpose:

- ✅ `deep-learning-gpu`, `hep-analysis`, `data-viz`
- ❌ `my-env`, `test`, `new-env`

### Troubleshooting

Common issues and solutions when working with pixi environments:

**Problem: Kernel not appearing in JupyterLab**

After creating a feature and adding `pixi-kernel`, the kernel doesn't show up in
the property inspector.

_Solution:_

1. Verify `pixi-kernel` was added to the feature:
   `pixi list -e my-env | grep pixi-kernel`
2. Reinstall the environment: `pixi install -e my-env`
3. Restart JupyterLab completely (stop and start the notebook server)
4. Make sure you've switched to the `pixi` kernel (not `ipykernel`)

**Problem: Package conflicts during installation**

You get conflicts when trying to add packages or import an environment.

_Solution:_

```bash
# Try updating the lock file
pixi update -f my-feature

# If conflicts persist, you may need to adjust version constraints
# Edit pixi.toml to relax version requirements, or
# Create a separate feature for conflicting packages
```

**Problem: Environment not appearing in property inspector**

You created an environment but it doesn't show up when you click the gear icon.

_Solution:_

The property inspector only shows environments that have `pixi-kernel`
installed:

```bash
# Add pixi-kernel to your feature
pixi add -f my-feature pixi-kernel
pixi install -e my-feature

# Restart the kernel in JupyterLab
```

**Problem: "No module named..." after switching environments**

You selected an environment in the property inspector but imports still fail.

_Solution:_

1. Save the notebook after selecting the environment
2. Restart the kernel (Kernel → Restart Kernel)
3. Verify the correct environment is active by checking the kernel name
4. Confirm the package is installed: `pixi list -e my-env | grep package-name`

**Problem: Running out of disk space**

Multiple environments are consuming too much storage.

_Solution:_

```bash
# Clean up cached packages
pixi clean

# Check disk usage
du -sh ~/.pixi

# Remove unused features by editing pixi.toml and running
pixi install

# Consider using the shared mlbase feature for common packages
```

**Problem: Import is slow or hangs**

`pixi import` or `pixi install` seems to hang or takes a very long time.

_Solution:_

- Large environments with many dependencies can take several minutes
- Check your internet connection (pixi downloads packages)
- If truly stuck, interrupt (<kbd>Ctrl</kbd>+<kbd>C</kbd>) and try again
- Consider breaking large environments into smaller, focused features

**Problem: Wrong Python version**

Your environment is using a different Python version than expected.

_Solution:_

Explicitly specify the Python version when creating the feature:

```bash
# For a specific version
pixi add -f my-feature "python=3.11"

# For a minimum version
pixi add -f my-feature "python>=3.11"
```

**Getting more help:**

If you encounter issues not covered here:

- Check the [pixi documentation](https://pixi.prefix.dev/latest/)
- Run `pixi --help` or `pixi <command> --help` for command-specific help
- See the [Getting Help](#getting-help) section for facility support

## Dask clusters

The `AB-STABLE` and `AB-DEV` images come set up for the AF's Dask Gateway, so
you can start a Dask cluster from the Dask sidebar or from code. See
[Dask Gateway](dask_gateway.md).

## Connecting an MCP client to your notebook

Once your JupyterLab is running you can attach an MCP
([Model Context Protocol](https://modelcontextprotocol.io/)) client — Claude
Code, Cursor, Continue, or any
[mcp-remote](https://www.npmjs.com/package/mcp-remote)-compatible client — so
the assistant can list, read, and execute cells in your live notebook session.

### Security & lifetime model (read this first)

The MCP server **runs inside your singleuser pod** — there is no shared service
to log into. That has two practical consequences:

| Surface                                                   | Endpoint                      | Token                                                                                          | Token lifetime                                           | Endpoint lifetime                                                                                                         |
| --------------------------------------------------------- | ----------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `https://jupyterhub.af.uchicago.edu` (BinderHub-launched) | `/user/<name>/proxy/3001/mcp` | JupyterHub API token, scope **`access:servers`** (or the tighter `access:servers!user=<name>`) | Durable — mint once at `/hub/token`, reuse across spawns | Only while your server is running; if culled, the endpoint 404s until you spawn again, then the **same** token reconnects |

This design is intentional: the credential is scoped to your identity, the
endpoint is scoped to one running compute. There is **no** independent,
always-on MCP service — that would break per-user isolation.

### From `https://jupyterhub.af.uchicago.edu` (BinderHub-launched notebooks)

**Step 1 — mint an API token.** Visit
[https://jupyterhub.af.uchicago.edu/hub/token](https://jupyterhub.af.uchicago.edu/hub/token),
create a token, and request the **`access:servers`** scope. If you want to
restrict the token to your own server only, use
**`access:servers!user=<your-username>`** instead — same connectivity, tighter
blast radius if the token leaks. Save the token in your password manager; you
will not see it again.

**Step 2 — configure your client.**

For Claude Code:

```bash
claude mcp add jupyter --transport http \
  https://jupyterhub.af.uchicago.edu/user/<your-username>/proxy/3001/mcp \
  --header "Authorization: Bearer <your-api-token>"
```

For Claude app, Cursor, Continue, or any client configured via
`claude_desktop_config.json` / `mcp_servers.json`:

```json
{
    "mcpServers": {
        "jupyter": {
            "command": "npx",
            "args": [
                "-y",
                "mcp-remote",
                "https://jupyterhub.af.uchicago.edu/user/<your-username>/proxy/3001/mcp",
                "--header",
                "Authorization:${JUPYTERHUB_AUTH_HEADER}"
            ],
            "env": {
                "JUPYTERHUB_AUTH_HEADER": "Bearer <your-api-token>"
            }
        }
    }
}
```

**Step 3 — make sure your server is running.** If MCP returns 404, your pod has
been culled. Open your JupyterHub home page to respawn; the same token will
reconnect immediately.

### What the MCP server can do

See the upstream
[Jupyter MCP Server documentation](https://jupyter-mcp-server.datalayer.tech/)
for the current tool surface (cell listing, cell read / insert / execute, kernel
control, document state).

## Getting help

For software additions, upgrades, or questions about the JupyterLab environment:

- See our [Getting Help](../getting_help.md) page for support options
- [Contact the UChicago facility team](../getting_help.md#facility-specific-support)
- [Open an issue](https://github.com/maniaclab/ml_platform/issues) for
  ml_platform image problems
