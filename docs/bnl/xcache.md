## Use the Xcache servers

Both BNL and SLAC have set up the **Xcache servers**, to help cache locally the
file on the grid or **CERN EOS**. Currently there are 60TB on the BNL Xcache
server, and 20TB on the SLAC Xcache server.

The Xcache servers

- provide **rucioN2N feature**, enabling users to access any files on the grid
  without knowing its exact site location and the file path.
- and help **cache locally** the content of remote files actually read in the
  first access, thus improves the read performance for sequential access. If
  only partial content of a file is read, then only that part would cached.

You can run the predefined command `Xcache_ls.py` to generate a clist file
(containing a list of physicsl file paths) for given datasets, then use the
clist in your jobs.

Run `Xcache_ls.py -h` to get the full usage

```bash
% Xcache_ls.py -h
Usage:
     Xcache_ls.py [options] dsetNamePattern[,dsetNamePattern2[,more patterns]]
  or
     Xcache_ls.py [options] --eos eosPath/
  or
     Xcache_ls.py [options] --eos eosPath/filenamePattern
  or
     Xcache_ls.py [options] dsetListFile

  This script generates a list (clist) of
  Xcache gLFN (global logical filename) access path
  for given datasets on Atlas grid sites.
  Wildcard is supported in the dataset name pattern.

Options:
  -h, --help            show this help message and exit
  -v                    Verbose
  -V, --version         print my version
  -X XCACHESITE, --XcacheSite=XCACHESITE
                        Specify a Xcache server site of BNL or SLAC
                        (default=BNL)
  -o OUTCLISTFILE, --outClistFile=OUTCLISTFILE
                        write the list into a file instead of the screen
  --eos=EOS_PATH, --cerneos=EOS_PATH
                        List files (*.root and *.root.[0-9] on default) on
                        CERN EOS
  -d OUTCLISTDIR, --dirForClist=OUTCLISTDIR
                        write the list into a directory with a file per
                        dataset
```

However, for large file inputs on the grid, you are recommended to plan ahead
and pre-stage them to BNL using
[R2D2 request](https://rucio-ui.cern.ch/r2d2/manage_quota) or rucio command.

## Using Xcache at BNL

Xcache enables to access data remotely and also to cache them locally for faster
access in future.

The Xcache server at BNL is `root://xrootd03.usatlas.bnl.gov:1094/`.

Let us take the input file used in the SLAC example. At SLAC, the `inputFile`
name for outside access (check the file `dset-outside.txt` at SLAC) is

```
inputFile=root://griddev03.slac.stanford.edu:2094//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/f9/bd/DAOD_SUSY15.11525262._000003.pool.root.1
```

For Xcache, we need add the Xcache server prefix with two slash characters, that
is,

```
inputFile=root://xrootd03.usatlas.bnl.gov:1094//root://griddev03.slac.stanford.edu:2094//xrootd/atlas/atlaslocalgroupdisk/rucio/data16_13TeV/f9/bd/DAOD_SUSY15.11525262._000003.pool.root.1
cd T3-Example-BNL/Interactive-Job
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```

### Using Xcache (gLFN) at BNL

Xcache at BNL also supports gLFN (global Logical File Name) access, without the
need of knowing the exact path of a given filename.

Let us take the same dataset used in the SLAC example.

```bash
$ rucio list-dataset-replicas data16_13TeV:data16_13TeV.00311481.physics_Main.merge.DAOD_SUSY15.f758_m1616_r8669_p3185_tid11525262_00
+-------------------------------+---------+---------+
| RSE                           |   FOUND |   TOTAL |
|-------------------------------+---------+---------|
| MWT2_UC_LOCALGROUPDISK        |      39 |      39 |
| OU_OSCER_ATLAS_LOCALGROUPDISK |      39 |      39 |
| AGLT2_LOCALGROUPDISK          |      39 |      39 |
| NERSC_LOCALGROUPDISK          |      39 |      39 |
| BNL-OSG2_LOCALGROUPDISK       |      39 |      39 |
| CERN-PROD_DATADISK            |      39 |      39 |
| NET2_LOCALGROUPDISK           |      39 |      39 |
| SLACXRD_LOCALGROUPDISK        |      39 |      39 |
| SWT2_CPB_LOCALGROUPDISK       |      39 |      39 |
| NET2_DATADISK                 |      39 |      39 |
+-------------------------------+---------+---------+
```

Let us to list the filenames in the dataset

```bash
$ rucio list-content $dset
+-------------------------------------------------------+--------------+
| SCOPE:NAME                                            | [DID TYPE]   |
|-------------------------------------------------------+--------------|
| data16_13TeV:DAOD_SUSY15.11525262._000003.pool.root.1 | FILE         |
| data16_13TeV:DAOD_SUSY15.11525262._000006.pool.root.1 | FILE         |
...
```

Let us take the second one file.

```bash
inputFile=root://xrootd03.usatlas.bnl.gov:1094//atlas/rucio/data16_13TeV:DAOD_SUSY15.11525262._000003.pool.root.1
../bin/Exam_JetsPlot $inputFile > myjob.log 2>&1
```

Enclosed is a screenshot of the condor running jobs.

```bash
$ condor_q
-- Schedd: spar0103.usatlas.bnl.gov : <130.199.48.19:9618?... @ 08/02/19 13:12:36
 ID       OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
35106.0   yesw2000        8/2  13:12   0+00:00:01 R  0    0.3 Exam_JetsPlot

Total for query: 1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0
suspended Total for yesw2000: 1 jobs; 0 completed, 0 removed, 0 idle, 1 running,
0 held, 0 suspended Total for all users: 2 jobs; 0 completed, 0 removed, 0 idle,
2 running, 0 held, 0 suspended
```
