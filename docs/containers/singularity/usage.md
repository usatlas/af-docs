# Using Singularity

## Singularity Usage Help

You can find the Singularity usage by running **singularity -h**

??? example "cent7a(SLAC)$ singularity -h"

    ```
    Linux container platform optimized for High Performance Computing (HPC) and
    Enterprise Performance Computing (EPC)

    Usage: singularity [global options...]

    Description: Singularity containers provide an application virtualization layer
    enabling mobility of compute via both application and environment portability.
    With Singularity one is capable of building a root file system that runs on any
    other Linux system where Singularity is installed.

    Options: -d, --debug print debugging information (highest verbosity) -h, --help
    help for singularity --nocolor print without color output (default False) -q,
    --quiet suppress normal output -s, --silent only print errors -v, --verbose
    print additional information --version version for singularity

    Available Commands: build Build a Singularity image cache Manage the local cache
    capability Manage Linux capabilities for users and groups config Manage various
    singularity configuration (root user only) delete Deletes requested image from
    the library exec Run a command within a container help Help about any command
    inspect Show metadata for an image instance Manage containers running as
    services key Manage OpenPGP keys oci Manage OCI containers plugin Manage
    Singularity plugins pull Pull an image from a URI push Upload image to the
    provided URI remote Manage singularity remote endpoints run Run the user-defined
    default command within a container run-help Show the user-defined help for an
    image search Search a Container Library for images shell Run a shell within a
    container sif siftool is a program for Singularity Image Format (SIF) file
    manipulation sign Attach a cryptographic signature to an image test Run the
    user-defined tests within a container verify Verify cryptographic signatures
    attached to an image version Show the version for Singularity

    Examples: $ singularity help <command> [<subcommand>] $ singularity help build $
    singularity help instance start

    For additional help or support, please visit https://www.sylabs.io/docs/
    ```

??? example "attsub01(BNL)$ singularity -h"

    ```
    USAGE: singularity [global options...] <command> [command options...] ...

         GLOBAL OPTIONS:
           -d|--debug    Print debugging information
           -h|--help     Display usage summary
           -s|--silent   Only print errors
           -q|--quiet    Suppress all normal output
              --version  Show application version
           -v|--verbose  Increase verbosity +1
           -x|--sh-debug Print shell wrapper debugging information

    GENERAL COMMANDS: help Show additional help for a command or container selftest
    Run some self tests for singularity install

    CONTAINER USAGE COMMANDS: exec Execute a command within container run Launch a
    runscript within container shell Run a Bourne shell within container test Launch
    a testscript within container

    CONTAINER MANAGEMENT COMMANDS: apps List available apps within a container
    bootstrap _Deprecated_ use build instead build Build a new Singularity container
    check Perform container lint checks inspect Display container's metadata mount
    Mount a Singularity container image pull Pull a Singularity/Docker container to
    $PWD

    COMMAND GROUPS: image Container image command group instance Persistent instance
    command group

    CONTAINER USAGE OPTIONS: see singularity help <command>

    For any additional help or support visit the Singularity website:
    https://www.sylabs.io/
    ```

The most frequently used commands are: **run**, **exec**, **shell** and
**pull**.

For additional help or support, please visit <https://www.sylabs.io/docs/>. For
quick start of version-3.5, you can refer to
[the User Guide](https://sylabs.io/guides/3.5/user-guide/quick_start.html#).

## Cache Folders

To make downloading images for build and pull faster and less redundant,
Singularity uses a caching strategy. By default, Singularity will create a set
of **cache folders** in your **$HOME** directory for docker layers, Cloud
library images, and metadata, respectively:

- $HOME/.singularity/cache/library
- $HOME/.singularity/cache/oci
- $HOME/.singularity/cache/oci-tmp

which could take quite much space, depending the image size.

You can set the envvar **SINGULARITY_CACHEDIR** to use other directory than the
default cache directory **$HOME/.singularity/cache**.

## Binding Paths and Mounts

On default, Singularity will map the following directories on your host system
to directories within the container:

- $HOME
- $PWD
- /tmp
- /proc
- /sys
- /dev

You can bind additional directories with option **-B | --bind**, such as:

- `-B /data`: map /data on the host to /data on the container
- or `-B /usr/local/share:/share,/data` (please note the **comma delimiter**) :
  map /usr/local/share on the host to /share on the container, and map /data on
  the host to /data on the container.

You can also defined envvar **SINGULARITY_BINDPATH** (such as
`export SINGULARITY_BINDPATH="/data:/mnt"`) to bind paths.

## Examples

You need define the envvar **SINGULARITY_CACHEDIR** to a directory to have
enough space to accommodate the Singularity cache.

### Some Fun Exercise Examples

There are some fun exercises to play with the singularity command. One simple
example is "Hello World", which takes the container image from
[the Singularity container hub](https://singularity-hub.org/).

>     lxplus$ singularity run shub://vsoch/hello-world
>     INFO:    Downloading shub image
>      59.75 MiB / 59.75 MiB [================================] 100.00% 35.87 MiB/s 1s
>     INFO:    Convert SIF file to sandbox...
>     RaawwWWWWWRRRR!! Avocado!
>     INFO:    Cleaning up image...

Let us try another example of "cow say":

>     lxplus$ singularity run shub://GodloveD/lolcow
>     INFO:    Downloading shub image
>      87.57 MiB / 87.57 MiB [================================] 100.00% 49.57 MiB/s 1s
>     INFO:    Convert SIF file to sandbox...
>      _______________________________
>     < Keep it short for pithy sake. >
>      -------------------------------
>             \   ^__^
>              \  (oo)\_______
>                 (__)\       )\/\
>                     ||----w |
>                     ||     ||
>     INFO:    Cleaning up image...

The similar container image is also available on
[the Docker Image Hub](https://hub.docker.com/).

>     lxplus$ singularity run docker://godlovedc/lolcow
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

As you see, singularity need download and convert the docker image into a
singularity image file (sif) first.

Singularity version 3 also supports container images on
[the Singularity container library](https://cloud.sylabs.io/library), which is
not supported in Singularity version 2. Let us check what is the latest in the
Ubuntu container on the library:

>     lxplus$ singularity -q exec library://ubuntu cat /etc/os-release
>     NAME="Ubuntu"
>     VERSION="18.10 (Cosmic Cuttlefish)"
>     ID=ubuntu
>     ID_LIKE=debian
>     PRETTY_NAME="Ubuntu Cosmic Cuttlefish (development branch)"
>     VERSION_ID="18.10"
>     HOME_URL="https://www.ubuntu.com/"
>     SUPPORT_URL="https://help.ubuntu.com/"
>     BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
>     PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
>     VERSION_CODENAME=cosmic
>     UBUNTU_CODENAME=cosmic

### Using Containers for LaTeX

If you need LaTeX and do not have LaTeX installed locally, you can use LaTeX
container images.

Let us pull the image and convert into sif first, and use the converted image
locally:

>     lxplus$ singularity pull latex.sif docker://dockershelf/latex
>     lxplus$ singularity shell latex.sif
>     INFO:    Convert SIF file to sandbox...
>     Singularity> pdflatex --version
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

>     lxplus$ singularity pull pdflatex.sif docker://astrotrop/pdflatex
>     lxplus$ singularity shell pdflatex.sif
>     INFO:    Convert SIF file to sandbox...
>     Singularity> pdflatex --version
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

For example, you can use sklearn containers.

> ```
> lxplus$  singularity pull sklearn.sif docker://fastgenomics/sklearn:0.19.1-p36-v5
> lxplus$  singularity shell sklearn.sif
> Singularity> python3
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
>     singularity shell /cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlasml/atlasml-base:py-3.7.2
>     Singularity> python3
>     >>> import sklearn
>     >>> import torch

As the container path `/cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlasml/`
indicates, the above container is also available on the docker hub via
**docker://**. But it would take quite a while (about an hour) to pull the
container from the docker hub and convert into a singularity image file (sif).

## Using Containers through ARLB

Prior to use ATLAS_LOCAL_ROOT_BASE (ALRB), you need install CVMFS first. Please
refer to [the CernVM-FS Client Quick Start](https://cernvm.cern.ch/fs/) at CERN
for CVMFS installation guide.

Then define the command **setupATLAS** as follows:

    export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
    alias setupATLAS='source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh'

The option **-c** in the command **setupATLAS** takes container location or
container keyword such as **slc5**, **slc6**, **centos6**, or **centos7**.
Please check
[ALRB Containers](https://twiki.atlas-canada.ca/bin/view/AtlasCanada/Containers)
for more details.

For example, start a centos7 container with ALRB setup:

>     lxplus$ setupATLAS  -c centos7
>     ------------------------------------------------------------------------------
>     Singularity: 3.5.3
>     From: /usr/bin/singularity
>     ContainerType: atlas-default
>     singularity  exec  -e  -H /afs/cern.ch/user/y/yesw/.alrb/container/singularity/home.snlmtE:/alrb -B /cvmfs:/cvmfs -B /afs/cern.ch/user/y:/home -B /tmp/yesw:/srv /cvmfs/atlas.cern.ch/repo/containers/fs/singularity/x86_64-centos7 /bin/bash
>     ------------------------------------------------------------------------------
>     lsetup               lsetup <tool1> [ <tool2> ...] (see lsetup -h):
>      lsetup agis          ATLAS Grid Information System
>      lsetup asetup        (or asetup) to setup an Athena release
>      lsetup atlantis      Atlantis: event display
>      lsetup eiclient      Event Index
>      lsetup emi           EMI: grid middleware user interface
>      lsetup ganga         Ganga: job definition and management client
>      lsetup lcgenv        lcgenv: setup tools from cvmfs SFT repository
>      lsetup panda         Panda: Production ANd Distributed Analysis
>      lsetup pod           Proof-on-Demand (obsolete)
>      lsetup pyami         pyAMI: ATLAS Metadata Interface python client
>      lsetup root          ROOT data processing framework
>      lsetup rucio         distributed data management system client
>      lsetup views         Set up a full LCG release
>      lsetup xcache        XRootD local proxy cache
>      lsetup xrootd        XRootD data access
>     advancedTools        advanced tools menu
>     diagnostics          diagnostic tools menu
>     helpMe               more help
>     printMenu            show this menu
>     showVersions         show versions of installed software
>
>     %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
>     You are running on a RHEL7 compatible OS.
>     Please refer to these pages for the status and open issues:
>      For releases:
>       https://twiki.cern.ch/twiki/bin/view/AtlasComputing/CentOS7Readiness#ATLAS_software_status
>     %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
>
>
>     Singularity>

If you do not need either ALRB or CVMFS, then run with tag **+noalrb** and
**+nocvmfs** after the image location/keyword:

>     lxplus$ setupATLAS  -c centos7+noalrb+nocvmfs
>     ------------------------------------------------------------------------------
>     Singularity: 3.5.3
>     From: /usr/bin/singularity
>     ContainerType: atlas-default
>     singularity  exec  -e  -H /afs/cern.ch/user/y/yesw/.alrb/container/singularity/home.tbnhXz:/alrb -B /afs/cern.ch/user/y:/home -B /tmp/yesw:/srv /cvmfs/atlas.cern.ch/repo/containers/fs/singularity/x86_64-centos7 /bin/bash
>     ------------------------------------------------------------------------------
>     Singularity>

If you like to start a Ubuntu OS without ALRB but with CVMFS, then run with tag
**+noalrb** after the mage location/keyword:

>     lxplus$ setupATLAS -c library://ubuntu+noalrb
>     INFO:    Convert SIF file to sandbox...
>     INFO:    Cleaning up image...
>     ------------------------------------------------------------------------------
>     Singularity: 3.5.3
>     From: /usr/bin/singularity
>     ContainerType: non-atlas
>     singularity  exec  -e  -H /afs/cern.ch/user/y/yesw/.alrb/container/singularity/home.Ion4ZS:/alrb -B /cvmfs:/cvmfs -B /afs/cern.ch/user/y:/home -B /tmp/yesw:/srv library://ubuntu /bin/bash
>     ------------------------------------------------------------------------------
>     INFO:    Convert SIF file to sandbox...
>      setupATLAS is available.
>
>     Singularity>
