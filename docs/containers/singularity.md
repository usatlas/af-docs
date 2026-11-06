# Singularity (Deprecated)

!!! warning "Singularity is deprecated"

    Sylabs, the company behind SingularityCE and the Sylabs Container Library
    (`cloud.sylabs.io`), has shut down. The Singularity Hub (`shub://`) has also
    been retired. Please use **[Apptainer](apptainer/introduction.md)** instead.

**[Apptainer](https://apptainer.org/)** is the Linux Foundation continuation of
the open-source Singularity project, and is what the BNL, SLAC and UChicago
analysis facilities and `setupATLAS -c` use today.

## Migrating from Singularity

Most workflows carry over unchanged:

- Existing Singularity images (`.sif`) and definition files work with Apptainer.
- Replace the `singularity` command with `apptainer` (most installations still
  provide `singularity` as an alias).
- Replace `SINGULARITY_*` environment variables with `APPTAINER_*` (for example,
  `SINGULARITY_CACHEDIR` becomes `APPTAINER_CACHEDIR`, and
  `SINGULARITY_BINDPATH` becomes `APPTAINER_BINDPATH`).
- Replace `shub://` and `library://` image sources with `docker://` or `oras://`
  sources.

See
[Singularity Compatibility](https://apptainer.org/docs/user/main/singularity_compatibility.html)
for the full list of differences.

## Apptainer Documentation

- [Introduction](apptainer/introduction.md)
- [Installation](apptainer/installation.md)
- [Usage](apptainer/usage.md)
- [Images](apptainer/images.md)
- [Grid Jobs](apptainer/grid.md)
