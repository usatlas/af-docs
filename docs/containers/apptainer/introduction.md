# Introduction

## What are Containers?

**Containers** are an operating system virtualization technology used to package
applications and their dependencies and run them in isolated environments.
Unlike Virtual Machines, they share the host OS kernel, thus provide a
**lightweight** method of packaging and deploying applications in a standardized
way across many different types of infrastructure.

## What is Singularity?

**[Singularity](https://sylabs.io/)** is a container platform. It allows you to
create and run containers that package up pieces of software in a portable and
reproducible way. In contrast to **[Docker](https://docs.docker.com/)**,
Singularity does not give superuser privileges. And it can access to the GPU on
a host node in native speed.

You can build a container using Singularity on your laptop, and then run it on
the grid. You can also make use of many already existing container images from
different sources.
