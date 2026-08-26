# OS Containers

CERN machines and many other grid sites have already running AlmaLinux9 OS,
while some sites may still be running CentOS7 OS.

Old ATLAS releases are built on top of CentOS7 OS, while many latest releases
are built on top of AlmaLinux9 OS. In order to run AlmaLinux9-based releases on
CentOS7 machines, and to run CentOS7-based releases on AlmaLinux9 machines, OS
Containers should be used.

## Interactive Running of OS Containers

On a CentOS7 machine, the following command should be used to run
AlmaLinux9-based ATLAS releases:

```shell
setupATLAS -c alma9
asetup main,AnalysisBase,latest

```

On an AlmaLinux9 machine, the following command should be used to run
CentOS7-based ATLAS releases:

```shell
setupATLAS -c centos7
asetup AnalysisBase,24.2.11
```

## Batch Running of OS Containers

However, the above commands do not work in batch mode. This is because the
command for running a container terminates the container after it completes its
execution. As a result, any subsequent commands in the batch will run on the
host machine instead of inside the container.

The command `setupATLAS -c` supports the option `-r` (or export
`ALRB_CONT_RUNPAYLOAD`) to run container payload commands. That is:

```shell
setupATLAS -c centos7 -r "source /srv/myPayload.sh"

```

Where the payload script `myPayload.sh` is something like:

```shell
asetup AnalysisBase,24.2.11
python3 ...
[...]
```
