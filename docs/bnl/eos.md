# Access to CERN EOS from BNL

The ways to list, write and read files on CERN EOS, documented
[here](https://twiki.cern.ch/twiki/bin/view/AtlasComputing/ATLASStorageAtCERN#EOS_storage_system),
still work at BNL, but you need specify the full EOS server name
**eosatlas.cern.ch** and obtain a CERN Kerberos ticket.

You can obtain and cache a CERN Kerberos ticket (this is also required for the
way of using ssh-tunnel below) by:

```bash
kinit YourNameAtCERN@CERN.CH
```

!!! warning "CERN.CH must be uppercase"

    Please be aware that in the above command the realm **CERN.CH** must be in
    **UPPERCASE**.

As convenience for the US ATLAS users, we have installed the eos-client and
eos-fusex packages on the interactive nodes.

After obtaining your CERN Kerberos ticket, you can access both the **ATLAS EOS**
and **USER EOS** instances.

To list your files:

```bash
ls /eos/atlas/...
ls /eos/user/y/yesw/...
```

!!! note

    Please replace _"y/yesw"_ with your own username at CERN.

To copy files from EOS:

```bash
cp /eos/atlas/YourDir/YourFilename.root .
```

To copy files to your EOS area at CERN:

```bash
cp MyNewFile.xxx /eos/atlas/YourDir/MyNewFile.xxx
```

You can create new directories in your EOS area at CERN:

```bash
mkdir /eos/atlas/YourDir/NewDirectory
```

In addition, you can also use ssh-tunnel to **eosatlas.cern.ch**:

```bash
ssh -NfL 1094:eosatlas:1094 lxplus.cern.ch
```

Then you can list files on EOS:

```bash
xrdfs eosatlas.cern.ch ls /eos/atlas/..
xrdfs localhost ls /eos/atlas/..   # if using ssh-tunnel
```

To copy files from EOS:

```bash
xrdcp root://eosatlas.cern.ch//eos/atlas/YourDir/YourFilename.root .
xrdcp root://localhost//eos/atlas/YourDir/YourFilename.root .  # if using ssh-tunnel
```

Or you make use of the existing script **eos-copy.py**, which is an alias and
should have been defined for you upon login:

```bash
% which eos-copy.py
/atlasgpfs01/usatlas/group/scripts/eos-copy.py

% eos-copy.py -h
Usage: eos-copy.py [options] eos_source... local_dir
   eos-copy.py [options] eos_source... pnfs_dir

     widlcard such as "*.root" is allowed in the eos_source.

   This script uses xrdcp to copy files/dirs from CERN EOS to a local dir
or a BNL private dCache dir. A valid CERN AFS token is required.


Options:
  -h, --help  show this help message and exit
  --verbose   Print verbose info
  --version   Print the script version then exit
```

Or to read EOS files in ROOT:

```cpp
TFile *file = TFile::Open("root://eosatlas.cern.ch//eos/atlas/YourDir/YourFilename.root");
TFile *file = TFile::Open("root://localhost//eos/atlas/YourDir/YourFilename.root");  // if using ssh-tun
nel
```

## Access to CERN EOS in BNL batch jobs

The method using ssh-tunnel would not work in batch jobs, you need access them
directly with `root://eosatlas.cern.ch`. However, for protected EOS files, you
need pass your CERN Kerberos ticket to the batch machines in the following way:

1. First define one envvar **KRB5CCNAME** prior to running
   `kinit YourNameAtCERN@CERN.CH`:

    ```bash
    export KRB5CCNAME=$HOME/krb5cc_`id -u`
    ```

2. Then add the envvar **KRB5CCNAME** to your condor batch jobs.

## Access to CERN EOS through BNL Xcache server

If you need repeat access the same EOS files, you can make use of the BNL Xcache
server to speed up the reading speed for the sequential access.

Just use the option **--eos=EOS_PATH** in the script **Xcache_ls.py** to
generate the clist files for your EOS files at CERN. Please run **Xcache_ls.py
-h** for more details.
