# SSH login and batch info at BNL

You can find [the main SDCC page](https://www.sdcc.bnl.gov)

## Interactive Connection to BNL

### ssh Connection to the interactive nodes

At BNL, you need login to `ssh.sdcc.bnl.gov` first, then login to interactive
machines `attsub01`, ..., `attsub08` with command `ssh` or `rterm -i` without
argument (please refer to
[the following section for the rterm usage help](#connect-to-the-interactive-nodes-from-nx)
). You can add the following to file `$HOME/.ssh/config` on your laptop or local
machine, so you can login `attsub*` machines directly.

```
    host atlasgw
         hostname ssh.sdcc.bnl.gov
         user yourNameAtBNL

    host attsub*
       user yourNameAtBNL
       proxycommand ssh atlasgw nc %h %p
```

Replace `yourNameAtBNL` with your own username at BNL. So you should be able to
run the following to login any machine such as `attsub01` directly,

```sh
ssh attsub01
```

Please note that: the **gateway** was just changed to `ssh.sdcc.bnl.gov`.

These nodes should be used for debugging and testing code. To run your complete
analysis code you should take advantage of [the batch system](batch.md).

### File transfer from/to BNL machines

<!-- TODO: Consider adding file transfer content or removing this section if covered elsewhere -->

### Connection to NX servers at BNL

<!-- TODO: Consider extracting NX/NoMachine content to separate remote_desktop.md -->

If you want to use a graphical environment, you can use
[NoMachine](https://www.nomachine.com/getting-started-with-nomachine) client to
connect to these NX servers, `nx.sdcc.bnl.gov`, which allows you to save/restore
sessions from anywhere anytime. Please visit
[the NoMachine (NX) page at BNL](https://www.sdcc.bnl.gov/information/services/how-use-nx-sdcc)
for details. Besides the connection through NoMachine client, you can also
connect to the new NX servers on web browsers, using the URL:
[https://nx.sdcc.bnl.gov](https://nx.sdcc.bnl.gov).

### Connect to the interactive nodes from NX

<!-- TODO: Consider extracting detailed rterm documentation to separate rterm.md or tools.md -->

After you connect to the NX server, you can open a konsole terminal (depending
on the Window Manager you have chosen). From NX servers, you can run `rterm` to
ssh to other attsub machines on a separate `xterm` terminal. `rterm` will help
choose **the least loaded node**.

Please find the detailed usage of `rterm` by running the command `rterm -h`.

```
    NX% rterm -h
    term, V1.502 - Written by J. Lauret 2001
    This command will open a connection to any remote node using the slogin
    command and, without node precision open a terminal on the least loaded
    node.

    Syntax is
      % rterm [Options] [NodeSpec] [UserName]

    Currently implemented options are :
      -i         interactive mode i.e. do not open an xterm but use slogin
                 directly to connect.
      -p port    use port number 'port' to connect
      -x node    Exclude 'node' from possible node to connect to. May be
                 a comma separated list of nodes.
      -funky     pick a color randomly
      -Y         use -Y option for ssh instead of -X

    The 'NodeSpec' argument may be a node name (specific login to a given node)
    or a partial node name followed by the '+' sign (wildcard). For example,
      % rterm attsub+

    will open a connection on the least loaded node amongst all batch-available
    attsub* nodes. By default, this command will determine the appropriate
    wildcarded node specification for your GroupID. However, if this help
    is displayed when the command '% rterm' is used, contact the RCF support
    team (your group ID is probably not supported by this script).

    The 'UserName' argument is also optional. If unspecified, it will revert
    to the current user ID.

    Finally, you may modify the xterminal layout by using the following
    environment variables
      TERM_BKG_COLOR     sets the xterm background color
      TERM_OPTIONS       sets any other xterm options
```

For example, you run define the envvar `TERM_OPTIONS` to pass the options the
executed command xterm.

```sh
% export TERM_OPTIONS="-bg black -fg green -fn 10x20"
% rterm
```

which will open a xterm terminal with the black background and green text color,
with the font size of 10x20.

### Setup ATLAS software environment

To make ATLAS-specific commands (including `setupATLAS`) available immediately
upon login, add the following lines to your shell startup script
(`$HOME/.bashrc` for bash users or `$HOME/.zshrc` for zsh users):

```bash
# Load USAtlas group environment at BNL
group_env=/atlasgpfs01/usatlas/group/group_env.sh
if [ -r "$group_env" ]; then
    . "$group_env"
fi
```

After you update your startup file, either log out and log back in, or source
the file manually:

```bash
source ~/.bashrc    # for bash
# or
source ~/.zshrc     # for zsh
```

From then on, the `setupATLAS` command will be available in all interactive
sessions.

Please be aware that the executable or library built on Alma9 machines cannot
run on CentOS7 machines because of the system glibc library difference. To use
old CentOS7 releases, you can use [singularity](https://sylabs.io/singularity/)
to compile your package(s) within CentOS7 container. The command
`setupATLAS -c CentOS7` could help set up such a container, you would get
something like:

```
     ------------------------------------------------------------------------------
    Info: /cvmfs mounted; do 'setupATLAS -d -c ...' to skip default mounts.
    ------------------------------------------------------------------------------
    Apptainer: 1.3.4
    Host: Linux, AlmaLinux 9.5 (Teal Serval), x86_64, 5.14.0-503.19.1.el9_5.x86_64
    From: /cvmfs/atlas.cern.ch/repo/containers/sw/apptainer/x86_64-el7/1.3.4/bin/apptainer
    ContainerType: atlas-default
    apptainer  exec  -e   -H /usatlas/u/yesw2000/.alrb/container/apptainer/home.XMrLKx:/alrb -B /cvmfs:/cvmfs -B /usatlas/u:/home -B /tmp/yesw:/srv /cvmfs/atlas.cern.ch/repo/containers/fs/singularity/x86_64-centos7 /bin/zsh
    ------------------------------------------------------------------------------
    lsetup               lsetup <tool1> [ <tool2> ...] (see lsetup -h):
     lsetup agis          ATLAS Grid Information System
     lsetup asetup        (or asetup) to setup an Athena release
     lsetup atlantis      Atlantis: event display
     lsetup eiclient      Event Index
     [...]
```

Then it would behave like that you work on a CentOS7 machine.
