# Using Apptainer

## Apptainer Usage Help

You can find the Apptainer usage by running `apptainer help`, or
`apptainer help <command>` for a specific command (such as
`apptainer help exec`).

The most frequently used commands are: `run`, `exec`, `shell` and `pull`.

For additional help or support, please visit
[the Apptainer User Guide](https://apptainer.org/docs/user/main/). For a quick
start, you can refer to
[the Quick Start guide](https://apptainer.org/docs/user/main/quick_start.html).

## Cache Folders

To make downloading images for build and pull faster and less redundant,
Apptainer uses a caching strategy. By default, Apptainer will create a set of
**cache folders** under `$HOME/.apptainer/cache` for OCI blobs, library images,
and metadata, which could take quite much space, depending the image size.

You can set the envvar `APPTAINER_CACHEDIR` to use other directory than the
default cache directory `$HOME/.apptainer/cache`. See
[Build Environment](https://apptainer.org/docs/user/main/build_env.html) for
more details.

## Binding Paths and Mounts

On default, Apptainer will map the following directories on your host system to
directories within the container:

- `$HOME`
- `$PWD`
- `/tmp`
- `/proc`
- `/sys`
- `/dev`

You can bind additional directories with option `-B | --bind`, such as:

- `-B /data`: map `/data` on the host to `/data` on the container
- or `-B /usr/local/share:/share,/data` (please note the **comma delimiter**) :
  map `/usr/local/share` on the host to `/share` on the container, and map
  `/data` on the host to `/data` on the container.

You can also defined envvar `APPTAINER_BINDPATH` (such as
`export APPTAINER_BINDPATH="/data:/mnt"`) to bind paths. See
[Bind Paths and Mounts](https://apptainer.org/docs/user/main/bind_paths_and_mounts.html)
for more details.

## Examples

You need define the envvar `APPTAINER_CACHEDIR` to a directory to have enough
space to accommodate the Apptainer cache.

### Some Fun Exercise Examples

There are some fun exercises to play with the `apptainer` command. One simple
example is "cow say", which takes the container image from
[the Docker Image Hub](https://hub.docker.com/).

>     lxplus$ apptainer run docker://godlovedc/lolcow
>     INFO:    Converting OCI blobs to SIF format
>     INFO:    Starting build...
>     Getting image source signatures
>     Copying blob 9fb6c798fa41 done
>     Copying blob 3b61febd4aef done
>     Copying blob 9d99b9777eb0 done
>     Copying blob d010c8cf75d7 done
>     Copying blob 7fac07fb303e done
>     Copying blob 8e860504ff1e done
>     Copying config 73d5b1025f done
>     Writing manifest to image destination
>     Storing signatures
>     2020/03/30 16:30:38  info unpack layer: sha256:9fb6c798fa41e509b58bccc5c29654c3ff4648b608f5daa67c1aab6a7d02c118
>     2020/03/30 16:30:41  info unpack layer: sha256:3b61febd4aefe982e0cb9c696d415137384d1a01052b50a85aae46439e15e49a
>     2020/03/30 16:30:41  info unpack layer: sha256:9d99b9777eb02b8943c0e72d7a7baec5c782f8fd976825c9d3fb48b3101aacc2
>     2020/03/30 16:30:41  info unpack layer: sha256:d010c8cf75d7eb5d2504d5ffa0d19696e8d745a457dd8d28ec6dd41d3763617e
>     2020/03/30 16:30:41  info unpack layer: sha256:7fac07fb303e0589b9c23e6f49d5dc1ff9d6f3c8c88cabe768b430bdb47f03a9
>     2020/03/30 16:30:41  info unpack layer: sha256:8e860504ff1ee5dc7953672d128ce1e4aa4d8e3716eb39fe710b849c64b20945
>     INFO:    Creating SIF file...
>     INFO:    Convert SIF file to sandbox...
>      ________________________________________
>     / Q: What do you call the scratches that \
>     | you get when a female                  |
>     |                                        |
>     \ sheep bites you? A: Ewe nicks.         /
>      ----------------------------------------
>             \   ^__^
>              \  (oo)\_______
>                 (__)\       )\/\
>                     ||----w |
>                     ||     ||
>     INFO:    Cleaning up image...

As you see, `apptainer` need download and convert the docker image into an
Apptainer image file (sif) first.

### Using Containers for LaTeX

If you need LaTeX and do not have LaTeX installed locally, you can use LaTeX
container images.

Let us pull the image and convert into sif first, and use the converted image
locally:

>     lxplus$ apptainer pull latex.sif docker://dockershelf/latex
>     lxplus$ apptainer shell latex.sif
>     INFO:    Convert SIF file to sandbox...
>     Apptainer> pdflatex --version
>     pdfTeX 3.14159265-2.6-1.40.20 (TeX Live 2019/Debian)
>     kpathsea version 6.3.1
>     Copyright 2019 Han The Thanh (pdfTeX) et al.
>     There is NO warranty.  Redistribution of this software is
>     covered by the terms of both the pdfTeX copyright and
>     the Lesser GNU General Public License.
>     For more information about these matters, see the file
>     named COPYING and the pdfTeX source.
>     Primary author of pdfTeX: Han The Thanh (pdfTeX) et al.
>     Compiled with libpng 1.6.37; using libpng 1.6.37
>     Compiled with zlib 1.2.11; using zlib 1.2.11
>     Compiled with xpdf version 4.01
>     .

There is another older PDFLaTex on the docker image hub, but with much more
packages installed.

>     lxplus$ apptainer pull pdflatex.sif docker://astrotrop/pdflatex
>     lxplus$ apptainer shell pdflatex.sif
>     INFO:    Convert SIF file to sandbox...
>     Apptainer> pdflatex --version
>     pdfTeX 3.14159265-2.6-1.40.15 (TeX Live 2014)
>     kpathsea version 6.2.0
>     Copyright 2014 Peter Breitenlohner (eTeX)/Han The Thanh (pdfTeX).
>     There is NO warranty.  Redistribution of this software is
>     covered by the terms of both the pdfTeX copyright and
>     the Lesser GNU General Public License.
>     For more information about these matters, see the file
>     named COPYING and the pdfTeX source.
>     Primary author of pdfTeX: Peter Breitenlohner (eTeX)/Han The Thanh (pdfTeX).
>     Compiled with libpng 1.6.10; using libpng 1.6.10
>     Compiled with zlib 1.2.8; using zlib 1.2.8
>     Compiled with poppler version 0.26.2

Then you can process your tex file inside the container.

### Using Containers for Machine Learning

There are many containers available for machine learning.

For example, you can use `sklearn` containers.

> ```
> lxplus$  apptainer pull sklearn.sif docker://fastgenomics/sklearn:0.19.1-p36-v5
> lxplus$  apptainer shell sklearn.sif
> Apptainer> python3
> >>> import sklearn
> >>> import pandas
> >>> import numpy
> >>> import scipy
> ```

Atlas also provides machine learning containers on CVMFS:

>     lxplus$ ls /cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlasml
>
>     atlasml-base:latest    ml-base:centos           ml-base:py-3.6.8
>     atlasml-base:py-3.6.8  ml-base:centos-py-3.6.8  ml-base:py-3.7.2
>     atlasml-base:py-3.7.2  ml-base:centos-py-3.7.2
>     ml-base:bionic         ml-base:latest
>     apptainer shell /cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlasml/atlasml-base:py-3.7.2
>     Apptainer> python3
>     >>> import sklearn
>     >>> import torch

As the container path `/cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlasml/`
indicates, the above container is also available on the docker hub via
`docker://`. But it would take quite a while (about an hour) to pull the
container from the docker hub and convert into an Apptainer image file (sif).

## Using Containers through ARLB

Prior to use `ATLAS_LOCAL_ROOT_BASE` (ALRB), you need install CVMFS first.
Please refer to [the CernVM-FS Client Quick Start](https://cernvm.cern.ch/fs/)
at CERN for CVMFS installation guide.

Then define the command `setupATLAS` as follows:

    export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
    alias setupATLAS='source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh'

The option `-c` in the command `setupATLAS` takes container location or
container keyword such as `slc5`, `slc6`, `centos6`, or `centos7`. Please check
[ALRB Containers](https://twiki.atlas-canada.ca/bin/view/AtlasCanada/Containers)
for more details.

For example, start a centos7 container with ALRB setup:

>     BNL$ setupATLAS -c CentOS7
>     ------------------------------------------------------------------------------
>     Info: /cvmfs mounted; do 'setupATLAS -d -c ...' to skip default mounts.
>     ------------------------------------------------------------------------------
>     Apptainer: 1.3.4
>     Host: Linux, AlmaLinux 9.5 (Teal Serval), x86_64, 5.14.0-503.19.1.el9_5.x86_64
>     From: /cvmfs/atlas.cern.ch/repo/containers/sw/apptainer/x86_64-el7/1.3.4/bin/apptainer
>     ContainerType: atlas-default
>     apptainer  exec  -e   -H /usatlas/u/yesw2000/.alrb/container/apptainer/home.XMrLKx:/alrb -B /cvmfs:/cvmfs -B /usatlas/u:/home -B /tmp/yesw:/srv /cvmfs/atlas.cern.ch/repo/containers/fs/singularity/x86_64-centos7 /bin/zsh
>     ------------------------------------------------------------------------------
>     lsetup               lsetup <tool1> [ <tool2> ...] (see lsetup -h):
>      lsetup agis          ATLAS Grid Information System
>      lsetup asetup        (or asetup) to setup an Athena release
>      lsetup atlantis      Atlantis: event display
>      lsetup eiclient      Event Index
>      [...]

If you do not need either ALRB or CVMFS, then run with tag `+noalrb` and
`+nocvmfs` after the image location/keyword:

>     lxplus$ setupATLAS -c centos7+noalrb+nocvmfs

If you like to start a Ubuntu OS without ALRB but with CVMFS, then run with tag
`+noalrb` after the image location/keyword:

>     lxplus$ setupATLAS -c docker://ubuntu+noalrb
