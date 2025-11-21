# XCache at UChicago

UChicago Analysis Facility maintains an XCache server, with 25 x 1.5 TB NVMes
and 2x25 Gbps NIC.

Users that want to access remote data of their own on EOS or elsewhere, can
manually add the prefix `root://xcache.af.uchicago.edu:1094//` to their root
paths and the prefix `:1094` to their server address, e.g.:

If the original path is:

```bash
root://someserver.org//atlaslocalgroupdisk/rucio/user/mgeyik/63/c4/user.mgeyik.26617246._000006.out.root
```

then make it:

```bash
root://xcache.af.uchicago.edu:1094//root://someserver.org:1094//atlaslocalgroupdisk/rucio/user/mgeyik/63/c4/user.mgeyik.26617246._000006.out.root
```

This will make the first access roughly twice slower, but following accesses
should be much faster and more reliable.

## High performance caching

ServiceX and large scale dask jobs require more performance and storage space
than a single XCache server can provide. For this reason at UChicago Analysis
Facility we have five dedicated nodes with 100Gbps NICs and NVMe only storage.

The optimal way to use them is to let Rucio decide which file should be accessed
through which XCache node in this way:

```bash
~> export SITE_NAME=AF_200
~> rucio list-file-replicas data18_13TeV:DAOD_PHYSLITE.34858087._000001.pool.root.1 --protocol root

| SCOPE        | NAME                                       | FILESIZE   | ADLER32   | RSE: REPLICA            |
| data18_13TeV | DAOD_PHYSLITE.34858087._000001.pool.root.1 | 264.466 MB | 41f423f0  | MWT2_UC_LOCALGROUPDISK: root://192.170.240.191:1094//root://fax.mwt2.org:1094//pnfs/uchicago.edu/atlaslocalgroupdisk/rucio/data18_13TeV/df/a4/DAOD_PHYSLITE.34858087._000001.pool.root.1 |
```

The way this works is that XCaches every 10 seconds send heartbeats and space
available to Rucio. Rucio then in real time calculates which XCache is optimal
for each file. While Rucio list-file-replicas call might be expensive, it
guarantees returned paths will work. If you still decide to cache list of the
paths, please keep in mind that available XCaches might change and you will have
to refresh it.
