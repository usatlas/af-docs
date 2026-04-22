# Batch System at UChicago

The UChicago Analysis Facility uses
[HTCondor](https://htcondor.readthedocs.io/en/latest/users-manual/index.html)
for batch workloads. To submit a job you need an executable script and a submit
file that describes your job.

## Before you start

Before submitting jobs, review the following:

- [ ] Which filesystem should be used to submit my jobs?
- [ ] Should my jobs go to the short or long queue?
- [ ] How much memory do my jobs need?
- [ ] Always check my jobs' status after submitting

For information about storage quotas and filesystem usage, see
[Data Storage](storage.md).

!!! warning "$HOME quota"

    Your quota at $HOME is 100GB. Be careful not to exceed this quota because some
    issues may arise, for example not being able to login next time.

    **Tips:**

    - Use the command `du -sh` to know the actual size of your current directory
    - Check the table displayed at the start of your session, which indicates the
      usage of your /home and /data directories

## Quick start

Define an executable script and a submit file that describes your job.

Job script, `myjob.sh`:

```bash
#!/bin/bash
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
export ALRB_localConfigDir=$HOME/localConfig
source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh
# at this point, you can lsetup root, rucio, athena, etc..
```

Submit file, `myjob.sub`:

```
Universe = vanilla

Output = myjob.$(Cluster).$(Process).out
Error = myjob.$(Cluster).$(Process).err
Log = myjob.log

Executable = myjob.sh

request_memory = 1GB
request_cpus = 1

Queue 1
```

Submit with `condor_submit`:

```
$ condor_submit myjob.sub
Submitting job(s).
1 job(s) submitted to cluster 17.
```

Check the queue with `condor_q`:

```
[lincolnb@login01 ~]$ condor_q


-- Schedd: head01.af.uchicago.edu : <192.170.240.14:9618?... @ 07/22/21 11:28:26
OWNER    BATCH_NAME    SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS
lincolnb ID: 17       7/22 11:27      _      1      _      1 17.0

Total for query: 1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended
Total for lincolnb: 1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended
Total for all users: 1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended
```

!!! warning "Do not use `watch condor_q`"

    Running `watch condor_q` polls the scheduler repeatedly and can overload it. Use
    `condor_watch_q` instead — it is an event-driven live view that is safe for
    continuous monitoring:

    ```sh
    condor_watch_q
    ```

## Job states

After submission, a job moves through several states visible in `condor_q`:

| State     | Code | Meaning                                                               |
| --------- | ---- | --------------------------------------------------------------------- |
| Idle      | I    | Waiting to be matched to an available slot                            |
| Running   | R    | Currently executing on a worker node                                  |
| Completed | C    | Finished (leaves the queue)                                           |
| Held      | H    | Interrupted due to an error; stays in queue until released or removed |
| Removed   | X    | Removed from queue via `condor_rm` (leaves the queue)                 |

`condor_q` shows jobs currently in the queue. Once a job leaves the queue
(completed or removed), use `condor_history` to look it up.

## Log, output, and error files

Each job should define three files in the submit file:

| File   | Submit attribute | Contents                                                                                                             |
| ------ | ---------------- | -------------------------------------------------------------------------------------------------------------------- |
| Log    | `log`            | When jobs were submitted, started, and stopped; resources used; exit status; where the job ran; interruption reasons |
| Output | `output`         | Stdout from your program (any print/display output)                                                                  |
| Error  | `error`          | Stderr captured by the operating system                                                                              |

!!! tip "Always define all three files"

    Defining `log`, `output`, and `error` in every submit file makes debugging much
    easier. The log file is especially valuable for right-sizing resource requests
    and diagnosing why jobs were held or evicted.

## Requesting resources

### Memory, CPUs, and disk

Request appropriate resources using the `request_memory`, `request_cpus`, and
`request_disk` submit attributes. It is very important to request the right
amount:

- **Too little**: HTCondor may hold your job (e.g. memory exceeded) or cause
  problems for other users sharing the same node.
- **Too much**: Your job will match fewer available slots, leading to longer
  wait times.

The best approach is to run a small test job first and check the **log file**
after it completes. HTCondor records actual resource usage at job termination:

```
005 (7195807.000.000) 05/19 14:35:56 Job terminated.
    (1) Normal termination (return value 0)
    ...
    Partitionable Resources :    Usage  Request Allocated
       Cpus                 :        0        1         1
       Disk (KB)            :       26     1024    995252
       Memory (MB)          :        1     1024      1024
```

Use the `Usage` column as a baseline for your real jobs (with some headroom for
safety). You can also adjust resource requests for already-queued jobs without
resubmitting using `condor_qedit`:

```
$ condor_qedit 128.0 RequestMemory 3072
Set attribute "RequestMemory".
```

### GPUs and multiple CPUs

For jobs that use multiple cores on a single machine, stay in the vanilla
universe — the parallel universe is not needed for single-node multi-core jobs:

```
request_cpus = 16
```

If GPU nodes are available in the pool, request them with:

```
request_gpus = 1
```

### Short and long queues

Before submitting jobs you should have an idea about how long the job will take
to finish (not the exact time but an approximate). In `HTCondor` we added a
feature called `shortqueue` with dedicated workers that will ONLY service jobs
that run for less than **4 hours**. To make use of the shortqueue you just have
to add the following configuration parameter to your job submit file.

```bash
+queue="short"
```

If your job is longer than 4 hours just exclude this configuration parameter
from your submit file and your jobs will be sent to the `long-queue`
automatically, otherwise your job will be placed on hold until you remove it or
release it (check HTCondor commands).

!!! tip "Use the short queue for short jobs"

    Using the short queue for short jobs when possible is essential for the use of
    the available resources, specially to let the long queue available for long
    jobs.

## Submitting multiple jobs

HTCondor can submit many independent jobs from a single submit file.

### Automatic variables

When you submit `queue N`, HTCondor assigns each job a `ClusterId` (shared by
the batch) and a `ProcId` (0 through N-1). Use these as variables in your submit
file to give each job unique filenames:

```
executable = analyze.exe
arguments  = file$(ProcId).in file$(ProcId).out
log        = job-$(ClusterId)-$(ProcId).log
output     = job-$(ClusterId)-$(ProcId).out
error      = job-$(ClusterId)-$(ProcId).err
queue 3
```

!!! note

    `$(Cluster)` and `$(Process)` are aliases for `$(ClusterId)` and `$(ProcId)` and
    may appear in older documentation.

### Queue statement variations

When your inputs are not numbered 0 to N-1, use one of these patterns:

**Match files by glob pattern:**

```
executable            = analyze.exe
arguments             = $(infile) $(infile).out
transfer_input_files  = $(infile)
log                   = job-$(ClusterId)-$(ProcId).log
output                = job-$(ClusterId)-$(ProcId).out
error                 = job-$(ClusterId)-$(ProcId).err
queue infile matching *.dat
```

Use `matching files *.dat` to match only files (not directories), or
`matching dirs job*` to match only directories.

**Explicit list:**

```
queue infile in (wi.dat ca.dat ia.dat)
```

**From a file (supports multiple variables):**

```
# job_list.txt contains:
# wi.dat, 2010
# wi.dat, 2015
# ca.dat, 2010
queue file,option from job_list.txt
```

Then reference `$(file)` and `$(option)` in the submit file.

!!! warning "Avoid multiple queue statements"

    Using multiple `queue` statements in a single submit file is not recommended and
    can cause unexpected behavior. Use `queue var from file` or
    `queue var in (list)` instead.

**Repeat jobs with `$(Step)`:** Submit 10 jobs per input file:

```
arguments = -i $(input) -rep $(Step)
queue 10 input matching files *.dat
```

## Organizing your jobs

When submitting many jobs, output files can quickly become hard to manage. Here
are some strategies.

### Grouping jobs with JobBatchName

By default, `condor_q` groups your jobs by cluster ID. You can assign a custom
name:

```
+JobBatchName = "MyAnalysis"
```

This makes the `BATCH_NAME` column in `condor_q` output more readable.

### Subdirectories by file type

Create subdirectories before submitting (HTCondor will not create them for you):

```
executable           = analyze.exe
transfer_input_files = input/file$(ProcId).in
log                  = log/job$(ProcId).log
queue 3
```

### Per-job directories with initialdir

Use `initialdir` to give each job its own directory containing the same
filenames (`file.in`, `job.log`, `file.out`). The executable should live in the
submit directory, not in the per-job directories:

```
executable           = analyze.exe
initialdir           = job$(ProcId)
arguments            = file.in file.out
transfer_input_files = file.in
log                  = job.log
output               = job.out
error                = job.err
queue 3
```

### Transferring shared input files

To transfer an entire directory of shared inputs, list the directory name:

```
# transfer whole directory (including the directory itself)
transfer_input_files = shared

# transfer directory contents only (not the directory itself)
transfer_input_files = shared/
```

### Selecting and renaming output files

By default, HTCondor transfers back all files created or modified in the job's
working directory. To transfer only specific files and optionally rename them:

```
transfer_output_files = results-final.dat, logs
transfer_output_remaps = "results-final.dat=results.dat"
```

## X509 proxy certificates

If you need to use an X509 Proxy, e.g. to access ATLAS Data, you will want to
copy your X509 certificate to the Analysis Facility. If you need a reminder,
information can be found in
[this link](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/WorkBookStartingGrid).

Store your certificate in `$HOME/.globus` and create a ATLAS VOMS proxy in the
usual way:

```bash
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
export ALRB_localConfigDir=$HOME/localConfig
source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh
lsetup emi
voms-proxy-init -voms atlas -out $HOME/x509proxy
```

You will want to generate the proxy on, or copy it to, the shared $HOME
filesystem so that the HTCondor scheduler can find and read the proxy. With the
following additions to your jobscript, HTCondor will configure the job
environment automatically for X509 authenticated data access:

```
use_x509userproxy = true
# Replacing <username> with your own username
x509userproxy = /home/<username>/x509proxy
```

E.g., in the job above for the user lincolnb:

```
Universe = vanilla

Output = myjob.$(Cluster).$(Process).out
Error = myjob.$(Cluster).$(Process).err
Log = myjob.log

Executable = myjob.sh

use_x509userproxy = true
x509userproxy = /home/lincolnb/x509proxy

request_memory = 1GB
request_cpus = 1

Queue 1
```

## Testing and troubleshooting

### Interactive jobs

Instead of running your executable, `condor_submit -i` opens a bash session
inside the job's execution environment — the same node, same container, same
transferred files. This is the fastest way to debug a job that is failing.

```sh
condor_submit -i submit_file.sub
```

You land in the job's working directory. Exit the shell when done.

### Live troubleshooting

To log into a **running** job and inspect it live:

```sh
condor_ssh_to_job <JobId>
```

This opens an SSH session on the execute node, in the job's working directory.

### Reviewing past jobs

`condor_history` is to completed jobs what `condor_q` is to queued jobs:

```sh
# All your past jobs:
condor_history
# A specific cluster:
condor_history 42
# With specific attributes:
condor_history -af ClusterId ProcId ExitCode MemoryUsage
```

## Common hold reasons and fixes

When a job is held, `condor_q -hold` shows the `HOLD_REASON`:

```sh
condor_q -hold
```

Common reasons and how to fix them:

**Job exceeded requested memory**

```sh
# Fix: increase RequestMemory without resubmitting
condor_qedit <JobId> RequestMemory 8192
condor_release <JobId>
```

**Incorrect path to input files**

Check `transfer_input_files` — all paths must be relative to the submit
directory or absolute paths that exist on the submit machine.

**Badly formatted bash script**

Windows line endings (`\r\n`) or a missing `#!/bin/bash` header will cause jobs
to be held. Fix with:

```sh
dos2unix my_script.sh
# or:
sed -i 's/\r$//' my_script.sh
```

Also ensure the script has `#!/bin/bash` on the first line and is executable
(`chmod +x my_script.sh`).

**Submit directory over quota**

```sh
du -sh ~
# Clean up output files, then:
condor_release <JobId>
```

**Administrator hold**

Contact the facility admins for the reason.

**Retry after fixing the issue:**

```sh
# Edit attributes without resubmitting:
condor_qedit <ClusterId> AttributeName NewValue
# Release held jobs:
condor_release <ClusterId>
# Or remove entirely:
condor_rm <ClusterId>
```

## Class Ads and job attributes

HTCondor stores information about jobs and machines as "Class Ads" — a set of
`AttributeName = value` pairs. You can inspect and filter using these attributes
directly from the command line.

**View all attributes for a job:**

```sh
condor_q -l <JobId>
```

**Select specific attributes (scriptable output):**

```sh
condor_q -af ClusterId ProcId MemoryUsage RemoteHost
```

**Filter jobs by attribute expression:**

```sh
condor_q -constraint 'JobStatus == 2 && RequestMemory > 4096'
```

**Useful job ClassAd attributes:**

| Attribute       | Description                            |
| --------------- | -------------------------------------- |
| `ClusterId`     | Cluster ID number                      |
| `ProcId`        | Process (job) ID within the cluster    |
| `JobStatus`     | 1=Idle, 2=Running, 4=Completed, 5=Held |
| `MemoryUsage`   | Current memory usage (MB)              |
| `RequestMemory` | Memory requested (MB)                  |
| `RemoteHost`    | Slot/machine the job is running on     |
| `UserLog`       | Path to the log file                   |
| `Iwd`           | Initial working directory              |
| `JobBatchName`  | Batch name set via `+JobBatchName`     |

**View machine/slot attributes:**

```sh
condor_status -l <SlotName>
condor_status -af Machine Cpus Memory
```

## Self-checkpointing

By default, an interrupted job (evicted, node failure) restarts from the
beginning. Self-checkpointing allows a job to resume from a saved state.

This is especially useful for:

- Very long-running jobs
- Jobs running on opportunistic (preemptible) resources

**How to implement:**

1. Edit your executable to periodically save intermediate state to a checkpoint
   file (e.g., every N iterations)
2. Edit your executable to check for and load a checkpoint file at startup
3. Add to your submit file:

```
when_to_transfer_output = ON_EXIT_OR_EVICT
```

This causes HTCondor to transfer output files back to the submit machine
whenever the job exits _or_ is evicted — so the checkpoint file survives
interruptions and is available when the job runs again.

## Automation

HTCondor can automatically manage jobs based on conditions defined in the submit
file, without requiring manual intervention.

### Automatic removal

Remove jobs that have been in the queue too long:

```
periodic_remove = (time() - QDate) > (100 * 3600)
```

Remove jobs that have been running too long:

```
periodic_remove = (JobStatus == 2) && (time() - EnteredCurrentStatus) > (2 * 3600)
```

### Automatic hold and release

Hold a job if it has been idle too long (e.g., waiting for a license):

```
periodic_hold = (JobStatus == 1) && (time() - EnteredCurrentStatus) > (3600)
```

Automatically release held jobs to retry transient failures:

```
periodic_release = (HoldReasonCode == 21)
```

### Actions on exit

Hold a job if it exits with a non-zero code:

```
on_exit_hold = (ExitCode != 0)
```

Remove a job only if it exits successfully:

```
on_exit_remove = (ExitCode == 0)
```

## Using Docker / Singularity containers (Advanced)

Some users may want to bring their own container-based workloads to the Analysis
Facility. We support both Docker-based jobs as well as Singularity-based jobs.
Additionally, the CVMFS repository unpacked.cern.ch is mounted on all nodes.

If, for whatever reason, you wanted to run a Debian Linux-based container on the
Analysis Facility, it would be as simple as the following Job file:

```
universe                = docker
docker_image            = debian
executable              = /bin/cat
arguments               = /etc/hosts
should_transfer_files   = YES
when_to_transfer_output = ON_EXIT
output                  = out.$(Process)
error                   = err.$(Process)
log                     = log.$(Process)
request_memory          = 1000M
queue 1
```

Similarly, if you would like to run a Singularity container, such as the ones
provided in the unpacked.cern.ch CVMFS repo, you can submit a normal vanilla
universe job, with a job executable that looks something like the following:

```bash
#!/bin/bash
singularity run -B /cvmfs -B /home /cvmfs/unpacked.cern.ch/registry.hub.docker.com/atlas/rucio-clients:default rucio --version
```

Replacing the `rucio-clients:default` container and `rucio --version` executable
with your preferred software.

## Reference

### Submit file attributes

| Option                  | What is it for?                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| transfer_output_files=  | When it isn't specified, it automatically transfers back all files that have been created or modified in the job's temporary working directory.                                                                                                                                                                                                                                        |
| transfer_input_files    | HTCondor transfer input files from the machine where the job is submitter to the machine chosen to execute the job                                                                                                                                                                                                                                                                     |
| when_to_transfer_output | <ul><li>on_exit: (default) when the job ends on its own</li><li>on_exit_or_evict: if the job is evicted from the machine</li></ul>                                                                                                                                                                                                                                                     |
| should_transfer_files   | <ul><li>yes: always transfer files to the remote working directory</li><li>if_needed: (default) access the files from a shared file system if possible, otherwise it will transfer the file</li><li>no: disables file transfer</li><li>command specifies whether HTCondor should assume the existence of a file system shared by the submit machine and the execute machine.</li></ul> |
| arguments               | options passed to the exe from the cmd line                                                                                                                                                                                                                                                                                                                                            |
| periodic_remove=time    | <ul><li>remove a job that has been in the queue for more than 100 hours e.g. (time() - QDate) > (100*3600)</li><li>remove jobs that have been running for more than two hours e.g. periodic_remove = (JobStatus == 2 ) && (time() - EnteredCurrentStatus) > (2*3600)</li></ul>                                                                                                         |
| queue                   | indicates to create a job                                                                                                                                                                                                                                                                                                                                                              |

### Commands

| Command                     | Description                                                                                                                      | Example |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------- |
| condor_hold                 | Put jobs in the queue into the hold state                                                                                        | -       |
| condor_dagman               | Meta scheduler for the HTCondor jobs within a DAG (directed acyclic graph) or multiple DAGs                                      | -       |
| condor_release              | Releases jobs from the HTCondor job queue that were previously placed in hold state                                              | -       |
| condor_ssh_to_job JobId     | Creates an ssh session to a running job                                                                                          | -       |
| condor_submit –interactive  | Sets up the job environment and input files. It gives a command prompt where you can then start job manually to see what happens | -       |
| condor_q                    | Display information about your jobs in queue                                                                                     | -       |
| condor_watch_q              | Event-driven live queue monitor (use this instead of `watch condor_q`)                                                           | -       |
| condor_qedit                | To modify job attributes. Check condor_q -long; condor_qedit Cmd = path_to_executable #changes it                                | -       |
| condor_q -long              | Check job's ClassAd attributes to edit the attributes                                                                            | -       |
| condor_q -analyze 27497829  | Determines why certain jobs are not running                                                                                      | -       |
| condor_q -hold 16.0         | Reason job 16.0 is in the hold state                                                                                             | -       |
| condor_q -hold user         | retrieves: ID, OWNER, HELD_SINCE, HOLD_REASON                                                                                    | -       |
| condor_q -nobatch           | retrieves: ID, OWNER, SUBMITTED, RUN_TIME, ST, PRI, SIZE, CMD                                                                    | -       |
| condor_q -run               | retrieves: ID, OWNER, SUBMITTED, RUN_TIME, HOST(S)                                                                               | -       |
| condor_q -factory -long     | Factory information and the jobMaterializationPauseReason attribute                                                              | -       |
| condor_tail xx.yy           | Displays the last bytes of a file in the sandbox of a running job                                                                | -       |
| condor_rm                   | Remove jobs from the queue (by user, cluster ID, or job ID)                                                                      | -       |
| condor_rm -all              | Remove all your jobs from the queue                                                                                              | -       |
| condor_history              | Review completed/past jobs that have left the queue                                                                              | -       |
| condor_status               | View pool resources — machine and slot information                                                                               | -       |
| condor_status -compact      | Summarized view of pool resources                                                                                                | -       |
| condor_q -af Attr1 Attr2    | Auto-format: display specific ClassAd attributes in a scriptable format                                                          | -       |
| condor_q -constraint 'expr' | Filter jobs by ClassAd expression                                                                                                | -       |
| condor_q -all               | View all users' jobs, not just your own                                                                                          | -       |

### Using condor within EventLoop

If you are using EventLoop to submit your code to the Condor batch system you
should replace your submission driver line with something like the following:

```cpp
EL::CondorDriver driver;
job.options()->setString(EL::Job::optCondorConf, "getenv = true\naccounting_group = group_atlas.<institute>");
driver.submitOnly( job, "yourJobName");
```
