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

#### Installing Additional Packages

You can install additional packages directly from your notebook with
[`pixi`](https://pixi.prefix.dev/latest/). The `ml_platform` image organizes
packages under features. For ML-related packages, use the `ml` feature (`-f ml`)
and install them with the `ml` environment (`-e ml`). If you are not using a
GPU-node, you can use the `mlcpu` environment which has the same set of packages
without the `cuda` system requirement.

**Example**: installing the GPU-version of `pytorch` along with `torchvision`
and `xgboost` available on [conda-forge](https://conda-forge.org/packages/), you
can run the following inside the notebook

```bash
pixi add -f ml pytorch-gpu torchvision xgboost
pixi install -e ml
```

For more advanced pixi usage, including custom environments and
multi-environment support, see the [pixi](#pixi) section below.

### AnalysisBase Images

- **AB-stable** - Based on AnalysisBase
- **AB-dev** - Based on AnalysisBase but with cutting edge uproot, dask, awkward
  arrays, etc.

!!! note "Other Images"

    Additional images may be available in the dropdown menu. Contact the facility
    team if you need a specific software environment not provided by these images.

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

- A **feature** is a named collection of packages (e.g., `ml`, `data-science`)
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
# Create a new feature with core packages
pixi add -f analysis numpy pandas matplotlib scipy

# Add more packages as needed
pixi add -f analysis uproot awkward hist

# Specify Python version if needed
pixi add -f analysis "python>=3.11"

# Register the kernel
pixi add -f analysis pixi-kernel
```

You can also combine packages from different channels:

```bash
# Add packages from specific channels
pixi add -f ml-custom -c pytorch -c conda-forge pytorch torchvision
pixi add -f ml-custom scikit-learn xgboost
pixi add -f ml-custom pixi-kernel
```

Once created, use the feature as a kernel following the same steps described in
the "Using your custom kernel in JupyterLab" section above.

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
pixi update -f ml pytorch torchvision

# Update all packages in a feature
pixi update -f ml

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

**When to use the simple approach** (add to `ml` or `mlcpu` feature):

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

# Consider using the shared ml environment for common packages
```

**Problem: Import is slow or hangs**

`pixi import` or `pixi install` seems to hang or takes a very long time.

_Solution:_

- Large environments with many dependencies can take several minutes
- Check your internet connection (pixi downloads packages)
- If truly stuck, interrupt (Ctrl+C) and try again
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

## Getting help

For software additions, upgrades, or questions about the JupyterLab environment:

- See our [Getting Help](../getting_help.md) page for support options
- [Contact the UChicago facility team](../getting_help.md#facility-specific-support)
- [Open an issue](https://github.com/maniaclab/ml_platform/issues) for
  ml_platform image problems
