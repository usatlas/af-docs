# Run Jobs on Grid

If you have already built your analysis package and tested it locally, you can
also submit the jobs to the grid and specify BNL/SLAC destination in prun option
**`--destSE`**.

---

## Setup of Panda Env

First you need set up the panda env:

```shell
setupATLAS     # if this command is already defined
lsetup panda   # Panda env setup
```

---

## Job Submission to the Grid

Then submit the jobs using the command **`prun`**

```shell
asetupTag=21.2.81,AnalysisBase
dset=data18_13TeV.00348885.physics_Main.deriv.DAOD_EXOT12.f937_m1972_p3553_tid14278917_00
prun --exec "bin/Exam_JetsPlot %IN" --bexec "build-it.sh" \
     --athenaTag=$asetupTag --cmtConfig $CMTCONFIG \         # specify the env needed for the job
     --inDS $dset --nFiles 2 --nFilesPerJob 1 \                                # input
     --outDS user.yesw.Tutorial-BNLT3.test5 --destSE BNL-OSG2_SCRATCHDISK   # output
```

Please **change the above output dataset name** and/ output RSE if needed.

Where the script **`build-it.sh`** is used to rebuild the analysis package on
the grid machines. The script read:

```sh
rm -f CMakeCache.txt
cmake src
make
```

For prun usage, please run **`prun -h`** or visit the
[wiki page](https://twiki.cern.ch/twiki/bin/view/PanDA/PandaRun "prun wiki page")

---

## Job Status

You can check the job status via command **`pbook`** or visit the
[panda job webpage](https://bigpanda.cern.ch/login/ "Panda Login Page").
