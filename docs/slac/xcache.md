# Using Xcache at SLAC

Xcache enables to access data remotely and also to cache them locally for faster
access in future.

The Xcache server at SLAC is `root://atlfax.slac.stanford.edu/`,

We will try to cache the files located at BNL. Let us take the input file used
in the BNL example. At BNL, the `inputFile` name is

```bash
inputFile=root://dcgftp.usatlas.bnl.gov:1096/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
```

The port `1096` is for inside access. For outside access, the port is `1094`.
So, the `inputFile` becomes

```bash
inputFile=root://dcgftp.usatlas.bnl.gov:1094/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
```

For Xcache, we need add the Xcache server prefix with two slash characters, that
is,

```bash
inputFile=root://atlfax.slac.stanford.edu//root://dcgftp.usatlas.bnl.gov:1094/pnfs/usatlas.bnl.gov/LOCALGROUPDISK/rucio/data18_13TeV/da/ea/DAOD_EXOT12.14278917._000001.pool.root.1
cd Interactive-Job
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```

## With gLFN

Xcache at SLAC also supports gLFN (global Logical File Name) access, without
knowing the exact path of a given filename.

Let us take the same dataset used in the BNL example.

```bash
$ rucio list-dataset-replicas $dset
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

```bash
$ rucio list-content $dset
+-------------------------------------------------------+--------------+
| SCOPE:NAME                                            | [DID TYPE]   |
|-------------------------------------------------------+--------------|
| data18_13TeV:DAOD_EXOT12.14278917._000001.pool.root.1 | FILE         |
| data18_13TeV:DAOD_EXOT12.14278917._000002.pool.root.1 | FILE         |
...
```

Let us take the second one file.

```bash
inputFile=root://atlfax.slac.stanford.edu//atlas/rucio/data18_13TeV:DAOD_EXOT12.14278917._000002.pool.root.1
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```
