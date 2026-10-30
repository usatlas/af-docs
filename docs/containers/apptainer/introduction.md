# Introduction

## What are Containers?

**Containers** are an operating system virtualization technology used to package
applications and their dependencies and run them in isolated environments.
Unlike Virtual Machines, they share the host OS kernel, thus provide a
**lightweight** method of packaging and deploying applications in a standardized
way across many different types of infrastructure.

## What is Apptainer?

**[Apptainer](https://apptainer.org/)** is a container platform. It allows you
to create and run containers that package up pieces of software in a portable
and reproducible way. In contrast to **[Docker](https://docs.docker.com/)**,
Apptainer does not give superuser privileges. And it can access to the GPU on a
host node in native speed.

Apptainer is the Linux Foundation continuation of the open-source Singularity
project. Existing Singularity images (`.sif`) and definition files work with
Apptainer, and most installations also provide `singularity` as an alias for
`apptainer`. See
[Singularity Compatibility](https://apptainer.org/docs/user/main/singularity_compatibility.html)
for details.

You can build a container using Apptainer on your laptop, and then run it on the
grid. You can also make use of many already existing container images from
different sources.
