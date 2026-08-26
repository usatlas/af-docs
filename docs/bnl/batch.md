# Batch System at BNL

## Condor batch system at BNL

RACF uses the Condor batch submission system. Detailed information about using
this system (submitting jobs, killing jobs, etc.) is described here:
[https://www.sdcc.bnl.gov/information/services/htcondor-sdcc](https://www.sdcc.bnl.gov/information/services/htcondor-sdcc).
You can also get the
[HTCondor Quick Start Guide](https://www.sdcc.bnl.gov/information/htcondor-quick-start-guide).

To see if you are on the list of authorized users to use the T3 batch system
consult this list of users:
[https://www.sdcc.bnl.gov/experiments/usatlas/list-users-institutes](https://www.sdcc.bnl.gov/experiments/usatlas/list-users-institutes)
You can also find your group information using the interactive command:
`whatgroup`.

If your name does not appear here,
[contact the BNL facility team](../getting_help.md#facility-specific-support).

After the batch system migration into the shared pool at BNL in March 2019, it
need not specify the group in the condor job description.

Since the upgrade of dCache in January 2019, xrootd access to dCache file would
require a valid grid proxy. You need specify `x509userproxy` in the job
description file with the following line:

```
x509userproxy = $ENV(X509_USER_PROXY)
```

after you have generated a valid grid proxy in an interactive machine.

Please notice that the envvar `X509_USER_PROXY` has already been defined to
`$HOME/x509_u$UID` upon login. So actually you can also just pass all the env
including the evnvar `X509_USER_PROXY` to the condor jobs without specifying
`x509userproxy`, with the following line in the job description file:

```
GetEnv          = True
```

In addition, please note that `rucio` is not recommended to be set up together
with Atlas releases because of possible conflict between them. You had better
use 2 separate sessions:

- One session with the rucio env, create grid proxy, and run rucio commands.
- The other session with one release env. submit condor jobs.

Or you could set up a rucio wrapper together with one release env, that is,

```sh
lsetup "asetup AnalysiisBase,21.2.129" "rucio -w"
```

which set up both the release env of AnalysisBase,21.2.129 and a rucio wrapper,
which does not provide python API.

### Use Condor within EventLoop

If you are using EventLoop to submit your code to the Condor batch system you
should replace your submission driver line with something like the following:

```cpp
EL::CondorDriver driver;
job.options()->setString(EL::Job::optCondorConf, "getenv = true\naccounting_group = group_atlas.<institute>");
driver.submitOnly( job, "yourJobName");
```

You need replace the institute `<institute>` with your own institute (as
assigned by ACF) here.

### Notice on using bash script in Condor

Please be aware that aliases will not be expanded by default within script. To
expand those aliases (such as `asetup`, `athena`), you need add the following
line to your script prior to running `setupATLAS`:

```sh
shopt -s expand_aliases
```

## HPC cluster at BNL

- [Getting cluster allocation](https://www.sdcc.bnl.gov/information/services/obtaining-cluster-access) -
  how to apply for cluster allocation for your project
- [Accessing clusters through the gateway](https://www.sdcc.bnl.gov/information/getting-started/accessing-clusters-through-gateway) -
  SSH gateways for SDCC cluster access
- [Slurm Workload Manager](https://www.sdcc.bnl.gov/information/services/slurm-workload-manager) -
  guides for Slurm access and usage
