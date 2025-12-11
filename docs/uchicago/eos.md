# Access to CERN-EOS from UChicago

/// warning | EOS access from worker nodes

`/eos` is only mounted on the interactive (`login`) nodes. To access from worker
nodes (e.g. condor jobs), we recommend the use of `xrootd` such as:

```bash
root://eosuser.cern.ch//eos/user/d/dschrute/file.txt
```

or via `xrdcp` inside your job:

```bash
xrdcp root://eosuser.cern.ch//eos/user/d/dschrute/file.txt .
xrdcp -r root://eosuser.cern.ch//eos/user/d/dschrute/many_files/ .
```

as long as you [export your CERN Kerberos ticket](#cern-kerberos-ticket) to your
HTCondor jobs.

///

The ways to list, write and read files on CERN EOS, documented
[here](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/ATLASStorageAtCERN#EOS_storage_system),
still work at UChicago.

/// note | setup-eos is deprecated

`setup-eos` has been deprecated in favor of using `kinit`.

///

On the Analysis Facility login servers, try the following:

```bash
kinit <cern_user>@CERN.CH
```

as described in [CERN Kerberos ticket](#cern-kerberos-ticket). To list your
files located at /eos/user/d/dschrute/:

```bash
ls /eos/user/d/dschrute
# remember to replace "d/dschrute" with your own username at CERN.
```

To create a new directory at /eos/user/d/dschrute/:

```bash
mkdir /eos/user/d/dschrute/new_dir
# remember to replace "d/dschrute" with your own username at CERN.
```

To copy files from /eos/user/d/dschrute/ to your current directory:

```bash
cp /eos/user/d/dschrute/file.txt .
# use `cp -r` if you need to copy a directory
```

To copy files from /eos/user/d/dschrute/ to any path within your profile:

```bash
cp /eos/user/d/dschrute/file.txt  $HOME/codingx/ # eg: /home/dschrute/codingx/
# use `cp -r` if you need to copy a directory
```

---

# CERN Kerberos ticket

You can obtain and cache a CERN Kerberos ticket (this is also required for the
way of using ssh-tunnel below) by doing:

/// warning | CERN.CH must be uppercase

Please notice that the domain CERN.CH must be in UPPERCASE.

///

```bash
kinit <name_at_CERN>@CERN.CH
```

List your ticket:

```bash
klist
```
