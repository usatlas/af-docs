# Data Sharing at BNL

This guide covers data sharing methods available at BNL, including Xcache
servers and accessing CERN EOS from BNL.

---

For XCache server usage and examples, see [XCache at BNL](xcache.md).

---

For accessing CERN EOS storage, see [CERN EOS Access](eos.md).

## Access to BNL files from CERN and outside BNL

You or your collaborators may need remote access to files at BNL.

### Access to BNL dCache files from CERN

You can use the following scripts (**~yesw/public/bnl/bnl_pnfs-ls.py**) to
generate clist or list files under a given BNL /pnfs directory.

```
lxplus% ~yesw/public/bnl/bnl_pnfs-ls.py -h
Usage:
     bnl_pnfs-ls.py [-o clistFilename] [options] [pnfsFilePath | pnfsDirPath] [morePaths]

  This script generates pfn (physical file name), pnfs-path,
or xrootd-path of files on BNL dcache for given datasets or files on PNFS,
where wildcard is supported in pnfsFilePath and pnfsDirPath

Options:
  -h, --help            show this help message and exit
  -v                    Verbose
  -V, --version         print my version
  -l, --listOnly        list only matched datasets under users dCache, no pfn
                        output
  -o OUTPFNFILE, --outPfnFile=OUTPFNFILE
                        write pfn list into a file instead of printing to the
                        screen
```

For example, you run the above script in the following ways:

```bash
lxplus% ~yesw/public/bnl/bnl_pnfs-ls.py -l /pnfs/usatlas.bnl.gov/users/yesw2000/testDir2
lxplus% ~yesw/public/bnl/bnl_pnfs-ls.py -o my.clist /pnfs/usatlas.bnl.gov/users/yesw2000/testDir2
1  files listed into clist file= my.clist
```

You can use the generated clist file in your job in the following way:

```cpp
TChain* chain = new TChain(treeName);
TFileCollection fc("fc","list of input root files","my.clist");
chain->AddFileInfoList(fc.GetList());
```

### Access to BNL other file systems from CERN

You can use **sshfs** to mount the remote BNL files to lxplus machines locally.
For example:

```bash
lxplus% mkdir /tmp/yesw/data
lxplus% sshfs attsub02:/atlasgpfs01/usatlas/data/yesw2000 /tmp/yesw/data
```

!!! note

    This assumes that you have already set up the ssh configuration as shown in
    [the section of interactive connection to BNL](accessing.md#ssh-connection-to-the-interactive-nodes).

To umount the mounted point, just run **fusermount -u /tmp/yesw/data**.

To list all the sshfs mounted points, just run **pgrep -a -f sshfs**.

### Access to BNL other file systems from other remote computers

For other computers outside of BNL such as your laptop, you can use the same way
as that for CERN. You can find the instruction of sshfs installation on
different OS at
[https://linuxize.com/post/how-to-use-sshfs-to-mount-remote-directories-over-ssh/](https://linuxize.com/
post/how-to-use-sshfs-to-mount-remote-directories-over-ssh/).
