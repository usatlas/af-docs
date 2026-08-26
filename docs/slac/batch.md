# Batch System at SLAC

## Submitting SLURM jobs at S3DF

All US ATLAS users can submit batch jobs using Slurm account **atlas:usatlas**,
e.g. `srun -A atlas:usatlas hostname`. The partition to use is `roma` (an HPC
cluster with AMD EPYC 7702 CPUs) and `ampere` (a Nvidia A100 GPU cluster). The
following is a typical script to be used with the `sbatch` command:

```bash
#!/bin/bash
#
#SBATCH --account=atlas:usatlas
#SBATCH --partition=ampere
#SBATCH --gpus a100:1
#SBATCH --job-name=my_first_job
#SBATCH --output=output-%j.txt
#SBATCH --error=output-%j.txt
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=2
#SBATCH --mem-per-cpu=4g
#SBATCH --time=0-00:10:00

unset KRB5CCNAME  # <-- this is important

export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase

# Use ALRB_CONT_CMDOPTS to provide bind mount, etc. options
export ALRB_CONT_CMDOPTS="-B /sdf,/fs,/lscratch"

# Use ALRB_CONT_RUNPAYLOAD to define the actual job payload
export ALRB_CONT_RUNPAYLOAD="source $HOME/myJobPayload.sh"

# Run the payload in container.
source $ATLAS_LOCAL_ROOT_BASE/user/atlasLocalSetup.sh -c el9 –pwd $PWD

```

Note that S3DF currently disabled submitting to multiple partitions (due to
issues with SLURM accounting). So please avoid submitting jobs using multiple
partitions (e.g. `#SBATCH --partition=roma,ampere`).

---

# Old obsolete systems

The following describe the soon to be obsolete systems, SDF and older AFS/LSF
facilities. Please migrate to S3DF ASAP as SLAC has set a deadline to
decommission most of the services in these two older facilities around
April 2024.

## Submit SLURM batch job at SDF

The [main page of SDF](https://sdf.slac.stanford.edu) provides
[basic info about using SLURM batch system](https://sdf.slac.stanford.edu/public/doc/#/batch-compute?id=using-slurm).
One of the lines in the example SLURM submission script,
`#SBATCH --partition=shared` allows you to specify `shared` or `usatlas`.

ATLAS owns a small fraction of CPU resource in SDF. `usatlas` will give you
guaranteed access to ATLAS owned CPUs but you may have to wait if other ATLAS
users are using them. `shared` allows you to access a large pool of CPUs (own by
others) so the waiting time will likely be shorter. But there is small risk that
your job will be pre-emptied. The rule of thumb is to run short jobs in
`shared`, and run long jobs (12h+) in `usatlas`.

To request a GPU, add `#SBATCH --gpus=1` to the submission script. All installed
GPUs are Nvidia CUDA GPUs. Since ATLAS currently does not own a GPU at SDF, you
will need to use `#SBATCH --partition=shared` if you request a GPU.

## Submit LSF batch jobs at AFS environment

The following page has many useful info about using the AFS environment,
esepecially with regard to using the LSF batch system.

`https://confluence.slac.stanford.edu/display/Atlas/SLAC+Analysis+Computing+Facility`
