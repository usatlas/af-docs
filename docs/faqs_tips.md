# FAQs and Tips for Tier3 users

### No valid kerberos after lxplus login.

If you have already had a valid CERN kerberos token on the local computer from
which you ssh to lxplus machines, you might see the following error message
after ssh login to CERN lxplus machines.

```
/usr/bin/xauth:  timeout in locking authority file /afs/cern.ch/user/y/yesw/.Xauthority
```

And it is very annoying that you have to run "kinit" again after ssh login.

To avoid the above trouble, you can:

- either add the option **"-k"** to the ssh command.
- or add **"GSSAPIDelegateCredentials yes"** into ~/.ssh/config on the local
  computer.

### Sharing the same kerberos token among the home clusters

By default, the kerberos token is cached locally on each individual machine, and
you have to run kinit each time on a different node. If those machines share the
same home directory, you can define the envvar **KRB5CCNAME** to
**$HOME/krb5cc\_`id -u`** prior to running **kinit**. So you would have a valid
kerberos token on other machines too.

For example, once you have obtained a valid CERN kerberos on one BNL attsub
machine, you can also access CERN EOS on other attsub machines without running
"kinit" again.

### Sharing one envvar KRB5CCNAME among multiple kerberos principals

The envvar **KRB5CCNAME** points to a file, it could only hold one kerberos
prinpical.

However, you can define it to point to a directory instead, then it could be
used for multiple kerberos pricipals. That is:

```
  export KRB5CCNAME=DIR:$HOME/.krb5cc
```

Please note the prefix **"DIR:"** before the directory name. And you need create
the directory **$HOME/.krb5cc** in advance.

### How to set up python3 env from CVMFS?

Run **showVersions python** after the ALRB setup (that is, running
_setupATLAS_). It will show the available python versions on CVMFS.

Then you can pick up one suitable for you, says, _3.8.8-x86_64-centos7_. So you
can run **lsetup "python 3.8.8-x86_64-centos7"** to set up that python3.

### How to set up Rucio with python3?

On default, rucio uses python2. So how to set up the rucio env to use in
python3?

You can run the following:

```
  export RUCIO_PYTHONBIN=python3
  lsetup rucio
```

### How to transfer QR code from Google Authenticator to other devices?

There are many services requiring **MFA** (Multi-Factor Authentication). It
would be more convenient to have multiple devices for MFA. To run the MFA app
and the service requiring MFA on the same computer, you can just copy and paste
the passcode into the service (such as BNL NX login), without checking your
phone to get the passcode and input the passcode into the service.

If you set up the MFA originally with Google Authenticator on your phone, and
want to add other devices (including computers) into the MFA list (for
convenience and backups), you need extract the secret code from Google
Authenticator, then add the secret code into other devices in the following:

1. Export the QR codes from "Google Authenticator" app

2. Read QR codes with QR code reader

3. Save the captured QR codes in a text file. Save each QR code on a new line.
   (The captured QR codes look like otpauth-migration://offline?data=...)

4. Call
   [this script on github](https://github.com/scito/extract_otp_secret_keys):

```
python extract_otp_secret_keys.py -p example_export.txt
```

### Problem in the Duo Mobile app

If you get the following error message with **Duo Mobile** app:

```
Authentication Restricted
Your administrator requires your phone to have a passcode
```

which may indicate that the screen lock is not enabled on your phone. You can
find the details [here](https://help.duo.com/s/article/3159?language=en_US).

So you can simply enable the screen lock on your phone to resolve the above
problem.

---

## Linux Basics

### Linux Process Control

Understanding how to control processes is crucial when working on remote
systems. Two commonly confused keyboard shortcuts are:

- <kbd>Control</kbd>+<kbd>C</kbd>: **Terminates** the current process by sending
  the SIGINT signal
- <kbd>Control</kbd>+<kbd>Z</kbd>: **Suspends** the current process by sending
  the SIGTSTP signal (does NOT terminate)

The key difference is that <kbd>Control</kbd>+<kbd>Z</kbd> only **suspends** the
process - it continues to exist and consume resources, just paused.
<kbd>Control</kbd>+<kbd>C</kbd> actually **kills** the process.

#### Working with Suspended Processes

When you suspend a process with <kbd>Control</kbd>+<kbd>Z</kbd>, you'll see
output like:

```
[1]+  Stopped                 python my_script.py
```

You can then manage suspended processes with these commands:

- `jobs`: List all suspended and background jobs
- `fg`: Bring the most recent suspended job to the foreground
- `fg %1`: Bring job number 1 to the foreground
- `bg`: Allow the most recent suspended job to run in the background
- `bg %1`: Allow job number 1 to run in the background
- `kill %1`: Terminate job number 1

#### Example Workflow

```sh
$ python my_script.py
# Press Control+Z to suspend
[1]+  Stopped                 python my_script.py

$ jobs
[1]+  Stopped                 python my_script.py

$ bg %1
[1]+ python my_script.py &

$ jobs
[1]+  Running                 python my_script.py &

$ kill %1
[1]+  Terminated              python my_script.py
```

<!-- prettier-ignore -->
!!! warning "Suspended Processes Still Consume Resources"

    Suspended processes remain in memory and can hold locks on files or resources.
    Always properly terminate processes you no longer need using `kill` or
    <kbd>Control</kbd>+<kbd>C</kbd>.

### Session Management with screen and tmux

When working on remote systems, it's crucial to understand that **closing your
SSH connection terminates all processes running in that session** unless you use
a terminal multiplexer like `screen` or `tmux`.

#### Why Use screen or tmux?

- Keep long-running processes alive after disconnecting
- Resume your work from a different location
- Protect against accidental disconnections
- Run multiple terminal sessions in one SSH connection

#### Using screen

**Start a new screen session:**

```sh
screen
```

**Start a named screen session:**

```sh
screen -S my_analysis
```

**Detach from a screen session:**

- Press <kbd>Control</kbd>+<kbd>A</kbd>, then <kbd>D</kbd>

**List all screen sessions:**

```sh
screen -ls
```

**Reattach to a screen session:**

```sh
screen -r
# Or for a specific session:
screen -r my_analysis
```

**Kill a screen session:**

```sh
# From inside the session:
exit
# Or from outside:
screen -X -S my_analysis quit
```

#### Using tmux

**Start a new tmux session:**

```sh
tmux
```

**Start a named tmux session:**

```sh
tmux new -s my_analysis
```

**Detach from a tmux session:**

- Press <kbd>Control</kbd>+<kbd>B</kbd>, then <kbd>D</kbd>

**List all tmux sessions:**

```sh
tmux ls
```

**Reattach to a tmux session:**

```sh
tmux attach
# Or for a specific session:
tmux attach -t my_analysis
```

**Kill a tmux session:**

```sh
tmux kill-session -t my_analysis
```

<!-- prettier-ignore -->
!!! danger "Don't Just Close Your SSH Connection"

    **Never** simply close your SSH connection or terminal window if you have
    important processes running. Always either:

    1. Use screen/tmux and properly detach
    2. Terminate your processes first with <kbd>Control</kbd>+<kbd>C</kbd> or `kill`
    3. Ensure processes are designed to run as background daemons

    Closing your connection without detaching from screen/tmux can cause session
    corruption or loss of work.

### Environment Variables and Shell Configuration

Environment variables control how your shell and programs behave. Understanding
these is important for configuring your analysis environment.

#### Common Environment Variables

- `$PATH`: List of directories where the shell looks for executable commands
- `$HOME`: Your home directory path
- `$USER`: Your username
- `$SHELL`: Your current shell (e.g., `/bin/bash` or `/bin/zsh`)
- `$PWD`: Present working directory
- `$OLDPWD`: Previous working directory

**View an environment variable:**

```sh
echo $PATH
```

**View all environment variables:**

```sh
env
# or
printenv
```

**Set an environment variable (current session only):**

```sh
export MY_VAR="value"
```

#### Shell Configuration Files

Your shell reads configuration files when starting. Understanding these helps
you customize your environment:

**For bash:**

- `~/.bash_profile`: Read when you login (e.g., SSH connection)
- `~/.bashrc`: Read when you start a new interactive shell
- `~/.bash_history`: Stores your command history

**For zsh:**

- `~/.zprofile`: Read when you login
- `~/.zshrc`: Read when you start a new interactive shell
- `~/.zsh_history`: Stores your command history

**Best practice:** In your `~/.bash_profile` (or `~/.zprofile` for zsh), source
your rc file:

```sh
# In ~/.bash_profile
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

This ensures your interactive shell settings are loaded for login shells too.

<!-- prettier-ignore -->
!!! tip "Testing Configuration Changes"

    After editing configuration files, either:

    - Start a new shell: `bash` or `zsh`
    - Source the file: `source ~/.bashrc` or `. ~/.bashrc`
    - Logout and login again

#### Modifying Your PATH

To add a directory to your PATH (e.g., for custom scripts):

```sh
# Add to ~/.bashrc or ~/.zshrc
export PATH="$HOME/bin:$PATH"
```

This prepends `~/bin` to your PATH, so executables there are found first.

### Process Monitoring

Monitoring your processes helps you track resource usage and identify problems.

#### Using top

The `top` command shows real-time process information:

```sh
top
```

**Useful top commands** (while running):

- <kbd>q</kbd>: Quit
- <kbd>k</kbd>: Kill a process (prompts for PID)
- <kbd>u</kbd>: Filter by username (useful to see only your processes)
- <kbd>M</kbd>: Sort by memory usage
- <kbd>P</kbd>: Sort by CPU usage

#### Using htop

`htop` is a more user-friendly alternative to `top` with color and interactive
features:

```sh
htop
```

Features:

- Color-coded output
- Mouse support (click to select processes)
- Tree view of process relationships
- Easy process killing with <kbd>F9</kbd>

#### Using btop

`btop` is a modern resource monitor with beautiful visualizations:

```sh
btop
```

Features:

- Modern, colorful interface
- Shows CPU, memory, disk, and network usage
- Process management
- Customizable themes

#### Finding Your Processes

**List all your processes:**

```sh
ps -u $USER
```

**Find processes by name:**

```sh
ps aux | grep python
```

**Kill processes by name:**

```sh
pkill python
# Or more specific:
pkill -f my_script.py
```

**Kill all your processes matching a pattern:**

```sh
pkill -u $USER -f "jupyter"
```

!!! warning "Be Careful with pkill"

    `pkill` can terminate multiple processes at once. Always double-check what
    you're killing:

    ```sh
    # First check what would be killed:
    pgrep -u $USER -f "pattern"
    # Then kill:
    pkill -u $USER -f "pattern"

```

```
