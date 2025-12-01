# Data Sharing at UChicago: Work between UChicago and CERN

This guide covers how to [use the XCache server](./xcache.md) and manage
authentication credentials for data access.

For accessing CERN EOS storage, see [CERN EOS Access](eos.md).

---

## Using XCache

See the [XCache documentation](./xcache.md) for information on using the XCache
server at UChicago.

---

## Always check your proxy certificate

If you are trying to use HTCondor driver to run some jobs that need data access
authorization, for example on `rucio`, always check the status of your proxy
grid certificate.

/// warning | Proxy certificate expiration

Your x509proxy certificate has an expiry date. Once it expires you have to
create an ATLAS VOMS proxy again in the usual way. You create (or copy it to)
the shared $HOME filesystem so that the HTCondor scheduler can find and read the
proxy.

///

This is how you copy it to $HOME:

```bash
voms-proxy-init -voms atlas -out $HOME/x509proxy
```

/// tip

Without the term `-out $HOME/x509proxy` you create a new proxy but the one that
maybe is already in your $HOME directory is still expired.

///

Once you renew your proxy certificate, add the following lines to your job
submit file so that HTCondor configures the job environment automatically for
x509 authenticated data access:

```bash
universe = vanilla

use_x509userproxy = true  # Required for x509 authentication
x509userproxy = /home/dschrute/x509proxy  # Path to your proxy certificate

request_memory = 1GB
request_cpus = 1
queue 1
```

---

## Accessing data directly from DDMs

If your dataset is available at the local DDM endpoint (eg.
MWT2_UC_LOCALGROUPDISK), it would be very performant to read the data directly
from it. First you need to discover paths to the files of your dataset. This is
easiest done in Rucio. You would issue a command like:

```bash
setupATLAS
lsetup rucio
rucio
# get your grid proxy
voms-proxy-init -voms atlas
rucio list-file-replicas user.ivukotic:xcache.test.dat --protocols root --pfns
```

This will give you a list of paths to all of the files and all the replicas in
your dataset. You can also limit it to only paths to specific RSES:

```bash
rucio list-file-replicas user.ivukotic:xcache.test.dat --protocols root --pfns --rses MWT2_UC_LOCALGROUPDISK
# output will look like this:
# root://fax.mwt2.org:1094//pnfs/uchicago.edu/atlaslocalgroupdisk/rucio/user/ivukotic/7d/9b/xcache.test.dat
```

If you need only some files, you can simply grep or awk for them and save
filepaths to txt file that your jobs will use.
