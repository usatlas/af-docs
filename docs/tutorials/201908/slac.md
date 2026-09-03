# Running the tutorial example at SLAC Tier3

_This page is Out of Date_

## Login to SLAC machines

First you need ssh to one Atlas centos7 machine at SLAC:

```shell
ssh -Y centos7.slac.stanford.edu
```

---

## Location of the analysis example package at SLAC

You can pull the analysis package under the same github repo directory or copy
from the directory at SLAC:

```
/nfs/slac/g/atlas/u02/yesw/T3-Example-SLAC
|-- 00-Readme.txt
|-- LSF-Job
|   `-- test-LSF.sh
`-- src
    |-- CMakeLists.txt
    `-- Exam_JetsPlot.cxx
```

where you can find:

- **`00-Readme.txt`**, the instruction file in text.
- **`test-LSF.sh`**, the script to submit LSF job.
- And the source code of the analysis package under subdir **`src/`**

---

## Env Setup and Package Building

### Setup of the Release Env

First set up the release env by:

```shell
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
source $ATLAS_LOCAL_ROOT_BASE/user/atlasLocalSetup.sh
asetup AnalysisBase,21.2.81
```

Next time you log in, you can simply run **`asetup`** under the same dir.

### Package Building

Then build the package by

```shell
cmake src  # generate Makefile
make          # compile & create executable file
```

---

## Dataset Preparation

To run the analysis, we need the following local input dataset.

```
dset=data16_13TeV.00311481.physics_Main.merge.DAOD_SUSY15.f758_m1616_r8669_p3185_tid11525262_00
```

To list the files in the dataset, set up rucio env and VO-atlas proxy first,
then run rucio to list files path.

```
lsetup rucio
voms-proxy-init -voms atlas
rucio list-file-replicas --rse SLACXRD_LOCALGROUPDISK --protocols root $dset > dset-outside.txt
sed 's#griddev03.slac.stanford.edu:2094#atlrdr1#' dset-outside.txt > dset-inside.txt
```

Let us look into the generated file `dset-inside.txt`
`$ head -5 dset-inside.clist`

```
+--------------+------------------------------------------+------------+-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| SCOPE        | NAME                                     | FILESIZE   | ADLER32   | RSE: REPLICA                                                                                                                                                        |
|--------------+------------------------------------------+------------+-----------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| data16_13TeV | DAOD_SUSY15.11525262._000003.pool.root.1 | 72.272 MB  | 9e258b16  | SLACXRD_LOCALGROUPDISK: root://atlrdr1//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/f9/bd/DAOD_SUSY15.11525262._000003.pool.root.1 |
| data16_13TeV | DAOD_SUSY15.11525262._000006.pool.root.1 | 70.782 MB  | a2844b00  | SLACXRD_LOCALGROUPDISK: root://atlrdr1//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/74/f9/DAOD_SUSY15.11525262._000006.pool.root.1 |
```

---

## Interactive Job Running at SLAC

Now we can run the job interactively

```shell
mkdir Interactive-Job
cd Interactive-Job
```

Take one input file in `dset-inside.txt`

```shell
inputFile=root://atlrdr1//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/f9/bd/DAOD_SUSY15.11525262._000003.pool.root.1
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```

It will write out an output file _`myOutputFile.root`_

Let us look into the output root file **`myOutputFile.root`**

```
$ root -l  myOutputFile.root
root [1] .ls
TFile**         myOutputFile.root
 TFile*         myOutputFile.root
  KEY: TH1D     h_njets_raw;1
  KEY: TH1D     h_mjj_raw;1
root [2] h_mjj_raw->Draw();
```

which will yield the plot

![](./plot-SLAC-interactive.png)

---

## Job Running on LSF at SLAC

SLAC uses LSF as its batch system. LSF batch jobs inherit your current working
environment (including both env variables & working directory).

```
cd LSF-Job
cat test-LSF.sh
```

Let us take a look of batch job script file **`test-LSF.sh`**

Please click the following filename to see its content

??? note "test-LSF.sh"

    ```sh
    # write both stdout and stderr into one log file
    log=$PWD/myjob.log
    exec &>$log

    inputFile=root://atlrdr1//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/7f/94/DAOD_SUSY15.11525262._000021.pool.root.1

    ../bin/Exam_JetsPlot $inputFile
    ```

Run the following command to submit the condor job

```shell
bsub -R 'centos7' -q atl-analq < test-LSF.sh
```

which will print to the screen with something like:

```
Job <468325> is submitted to queue atl-analq.
bjobs jobID    # check job status
```

Then you can run **`bjobs`** to check your job status.

```
$ bjobs 468325
JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
468325  yesw    RUN   atl-analq  cent7d      kiso0030    *inputFile Aug  2 11:28
```

The job in the above output is still running.

If the job has finished, you will see

```
$ bjobs 468325
JOBID   USER    STAT  QUEUE      FROM_HOST   EXEC_HOST   JOB_NAME   SUBMIT_TIME
468325  yesw    DONE  atl-analq  cent7d      kiso0030    *inputFile Aug  2 11:28
```

```
$ ls -l
total 100
-rw-r--r--. 1 yesw atlas  4468 Aug  2 11:28 myOutputFile.root
-rw-r--r--. 1 yesw atlas 87557 Aug  2 11:28 myjob.log
-rw-r--r--. 1 yesw atlas   239 Aug  2 11:22 test-LSF.sh
```

The files _`myjob.log`_ and _`myOutputFile.root`_ are created by the batch job,
and are being updated continuously during the job running, because the work
directory of the batch job is same as the directory from which the job was
submitted.

Similarly, you can also look into the output file _`myOutputFile.root`_ and make
plot.

![](./plot-SLAC-batch.png)

---

## Using Xcache at SLAC

Xcache enables to access data remotely and also to cache them locally for faster
access in future.

The Xcache server at SLAC is **`root://atlfax.slac.stanford.edu/`**,

We will try to cache the files located at BNL. Let us take the input file used
in the BNL example. At BNL, the `inputFile` name is

```
inputFile=root://dcgftp.usatlas.bnl.gov:1096/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
```

The port `1096` is for inside access. For outside access, the port is `1094`.
So, the `inputFile` becomes

```
inputFile=root://dcgftp.usatlas.bnl.gov:1094/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
```

For Xcache, we need add the Xcache server prefix with two slash characters, that
is,

```
inputFile=root://atlfax.slac.stanford.edu//root://dcgftp.usatlas.bnl.gov:1094/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
cd Interactive-Job
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```

### Using Xcache (gLFN) at SLAC

Xcache at SLAC also supports gLFN (global Logical File Name) access, without
knowing the exact path of a given filename.

Let us take the same dataset used in the BNL example.

Please click the following command (where **`dset`** is defined previously) to
see the output

??? note "rucio list-dataset-replicas $dset"

    ```
    DATASET: data18_13TeV:data18_13TeV.00348885.physics_Main.deriv.DAOD_EXOT12.f937_m1972_p3553_tid14278917_00
    +-------------------------+---------+---------+
    | RSE                     |   FOUND |   TOTAL |
    |-------------------------+---------+---------|
    | CERN-PROD_DATADISK      |      83 |      83 |
    | GRIF-LPNHE_DATADISK     |      83 |      83 |
    | BNL-OSG2_LOCALGROUPDISK |      83 |      83 |
    | MAINZ_LOCALGROUPDISK    |      83 |      83 |
    +-------------------------+---------+---------+
    ```

List just the filenames in the dataset

```
$ rucio list-content $dset
+-------------------------------------------------------+--------------+
| SCOPE:NAME                                            | [DID TYPE]   |
|-------------------------------------------------------+--------------|
| data18_13TeV:DAOD_EXOT12.14278917._000001.pool.root.1 | FILE         |
| data18_13TeV:DAOD_EXOT12.14278917._000002.pool.root.1 | FILE         |
...
```

Let us take the second one file.

```
inputFile=root://atlfax.slac.stanford.edu//atlas/rucio/data18_13TeV:DAOD_EXOT12.14278917._000002.pool.root.1
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```
