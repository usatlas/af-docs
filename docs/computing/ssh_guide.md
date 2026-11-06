# SSH Access Guide

This guide covers SSH key generation and setup for accessing US ATLAS Analysis
Facilities.

---

## Prerequisites

Before you begin, ensure you have:

- An approved user account at your chosen facility (see
  [Signing Up](../computing/index.md#before-you-begin))
- A terminal/command-line interface on your local machine
- Basic familiarity with command-line operations

---

## Choose Your Username Wisely

!!! tip "Username Best Practices"

    When choosing a UNIX username during signup, you should consider using the same
    username that you have at CERN. This will make certain tools (e.g. Rucio, EOS)
    simpler to set up. Otherwise, we recommend that you pick a username that is
    reflective of your full name, and that you should prefer brevity (e.g. John
    Smith -> `jsmith`). **We will not be able to easily change your username for you
    if you decide that you want something different later.**

---

## Generating SSH Keys

Once your account is approved, you will need to generate a cryptographic SSH key
pair consisting of:

- **Public key**: Upload to your facility profile
- **Private key**: Keep securely on your local machine

### Create an SSH Key Pair

On Mac/Linux, create an SSH key pair using the following command:

```sh
ssh-keygen -t ed25519
```

We recommend modern elliptic curve key types, such as `ed25519` or `ecdsa`.
These provide better security and performance than older key types.

!!! warning | Passphrase Recommended

    We strongly recommend setting a passphrase on your private key. This way, if
    your private key is ever lost or compromised, an attacker would still need your
    passphrase in order to impersonate you.

    **Note**: If you use VS Code Remote SSH, be aware that passphrases can cause
    connection issues. See the [VS Code guide](../tools/vscode.md) for workarounds.

### Key File Locations

The `ssh-keygen` command will generate two files in `~/.ssh` (unless you chose a
different location):

If you used the `ed25519` key type with the default location:

- **Private key**: `~/.ssh/id_ed25519`
- **Public key**: `~/.ssh/id_ed25519.pub`

### Do Not Store SSH Keys on Shared Clusters

The analysis facilities are **shared systems**. Your home directory lives on a
shared filesystem that is backed up, accessible to system administrators, and
exposed if your account or the system is ever compromised. For this reason:

!!! danger "Do Not Generate Keys on the Cluster for Other Services"

    Do **not** run `ssh-keygen` on a facility login node and then add the
    resulting public key to GitHub, CERN GitLab, another facility, or any other
    service. Doing so leaves a private key on a shared system that grants
    access to all of your repositories and accounts, often without a
    passphrase, and typically long after you have forgotten it is there.

    Likewise, never copy your personal private key (e.g. `~/.ssh/id_ed25519`)
    from your laptop to a facility.

Instead, use [agent forwarding](#agent-forwarding) so that tools like `git` on
the cluster can use the key held on your local machine.

!!! tip "Already Have a Key on the Cluster?"

    If you previously generated a key on a facility and added it to a service:

    1. Remove the corresponding public key from that service (for GitHub,
       under **Settings → SSH and GPG keys**).
    2. Delete the key pair from the cluster, e.g.
       `rm ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.pub`. Be careful not to remove
       `~/.ssh/authorized_keys` or `~/.ssh/config` if they exist.
    3. Set up [agent forwarding](#agent-forwarding) from your local
       machine instead.

---

## Uploading Your Public Key

### View Your Public Key

To view your public key for uploading:

```sh
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output (it should start with `ssh-ed25519`).

### Upload to Facility Portal

Upload your public key to your facility's user portal:

- **BNL**: Upload during account creation or via the SDCC portal
- **UChicago**: Paste into the "SSH public key" text box on
  [af.uchicago.edu](https://af.uchicago.edu/)
- **SLAC**: Follow facility-specific instructions

!!! danger "Important: Protect Your Private Key!"

    You must **only** upload the public key (.pub file)! Treat the private key as if
    it were your password. Never share it or upload it anywhere.

---

## SSH Configuration (Optional)

### Create SSH Config File

You can create an SSH config file to simplify connections. First, open or create
the `~/.ssh/config` file:

```sh
touch ~/.ssh/config
```

Then add an entry for your facility (example for UChicago):

```
Host uchicago
  HostName login.af.uchicago.edu
  User <your-username>
  ForwardAgent yes
  IdentityFile ~/.ssh/id_ed25519
```

Replace `<your-username>` with your actual username.

With this configuration, you can simply run:

```sh
ssh uchicago
```

instead of the full `ssh <username>@login.af.uchicago.edu`.

### SSH Agent Setup

`ssh-agent` is a small program that runs on your **local machine** and holds
your decrypted private keys in memory. You unlock your key once per session, and
`ssh` asks the agent to sign login requests on your behalf. Your private key
never leaves your machine, and you do not have to type your passphrase on every
connection.

To add your key to the agent:

```sh
ssh-add ~/.ssh/id_ed25519
```

You will be prompted for your passphrase once. To confirm the key was loaded,
list the keys the agent currently holds:

```sh
ssh-add -l
```

!!! tip "SSH Agent Error"

    If you get this error message:

    ```
    Could not open a connection to your authentication agent.
    ```

    You may need to start the SSH Agent first:

    ```sh
    eval "$(ssh-agent -s)"
    ```

    Then try the `ssh-add` command again.

!!! tip "Limit How Long Keys Stay Unlocked"

    You can give keys a lifetime so they are removed from the agent
    automatically, for example after 8 hours:

    ```sh
    ssh-add -t 8h ~/.ssh/id_ed25519
    ```

    To remove all keys from the agent immediately, run `ssh-add -D`.

### Agent Forwarding

Agent forwarding lets programs running on a remote host (such as `git` on a
login node) use the keys held by the agent on your local machine. This is the
recommended way to authenticate to services like GitHub or CERN GitLab from an
analysis facility, because no private key ever needs to exist on the cluster.

Enable it only for the facility hosts you trust by adding `ForwardAgent yes` to
that host's entry in `~/.ssh/config`, as in the UChicago example above.

Alternatively, you can enable forwarding for a single connection by passing the
`-A` flag to `ssh`:

```sh
ssh -A <username>@login.af.uchicago.edu
```

This is handy if you only occasionally need your keys on the remote host, since
forwarding stays off unless you ask for it.

Once connected, you can check that forwarding works:

```sh
ssh-add -l                # should list your local key(s)
ssh -T git@github.com     # should greet you by your GitHub username
```

If `ssh-add -l` on the remote host reports that it cannot connect to the agent,
make sure the key is loaded in your **local** agent and that you connected using
the host alias with `ForwardAgent yes` (or passed `-A`).

!!! warning "Only Forward Your Agent to Hosts You Trust"

    While you are connected, anyone with administrative (root) access on the
    remote host can ask your forwarded agent to sign requests as you. They
    cannot copy your private key, but they can use it for as long as your
    session is open. To limit this risk:

    - Set `ForwardAgent yes` only on specific `Host` entries, **never** under
      `Host *`.
    - Consider adding keys with `ssh-add -c`, which asks you to confirm on your
      local machine each time the key is used (requires an `ssh-askpass`
      program).
    - Disconnect sessions you are no longer using.

---

## Testing Your Connection

After uploading your public key, wait for the facility's synchronization period
(typically 15 minutes for UChicago, varies by facility), then test your
connection:

```sh
ssh <username>@<facility-hostname>
```

If it doesn't work:

1. Verify your account has been approved
2. Confirm you uploaded the public key (not private key)
3. Wait the full synchronization period
4. Check that you're using the correct hostname and username

If problems persist, see our [Getting Help](../getting_help.md) page for support
options.

---

## Facility-Specific Notes

Each facility may have specific SSH requirements or configurations:

- **BNL**: May require gateway/bastion host configuration
- **UChicago**: Does not accept deprecated key types (DSA, RSA with SHA-1)
- **SLAC**: Different login procedures for S3DF vs older systems

Refer to your facility's specific access guide for details:

- [Accessing BNL](../bnl/accessing.md)
- [Accessing UChicago](../uchicago/accessing.md)
- [Accessing SLAC](../slac/accessing.md)

---

## Accessing CERN Resources

If you need to access CERN resources like lxplus, GitLab, or SVN, you'll need
special SSH configuration that uses Kerberos authentication.

### SSH Configuration for CERN

Add the following to your `~/.ssh/config` file:

```sh
# CERN-wide settings
Host *.cern.ch
  User <CERN_USER>  # (1)!
  ForwardX11 yes
  GSSAPIAuthentication yes
  GSSAPIDelegateCredentials yes

# lxplus login nodes
Host lxplus
  HostName lxplus.cern.ch

Host lxplus7
  HostName lxplus7.cern.ch

Host lxplus*.cern.ch lxplus lxplus* aiatlasbm*
  PubkeyAuthentication no
  ForwardX11 yes

# CERN GitLab
Host gitlab.cern.ch
  ForwardX11 no

# CERN SVN
Host svn.cern.ch svn
  ForwardX11 no

# lxtunnel for proxy connections
Host lxtunnel lxtunnel.cern.ch
  HostName lxtunnel.cern.ch
  PubkeyAuthentication no
  ForwardX11 yes
  ControlPath ~/.ssh/controlmasters/%r@%h:%p
  ControlMaster auto
  ControlPersist 10m
  Protocol 2
  ServerAliveInterval 60
  ServerAliveCountMax 2
  DynamicForward 8090
```

1. Replace `<CERN_USER>` with your CERN username (e.g., `jsmith`)

!!! tip "Create ControlMaster Directory"

    For the lxtunnel configuration to work, create the ControlMaster directory:

    ```sh
    mkdir -p ~/.ssh/controlmasters
    ```

### Kerberos Authentication Setup

CERN resources use Kerberos for authentication. After configuring SSH, obtain a
Kerberos ticket:

```sh
kinit <CERN_USER>@CERN.CH
```

You'll be prompted for your CERN password. After obtaining a ticket, you can SSH
to CERN resources without additional password prompts:

```sh
ssh lxplus
```

To check your current Kerberos tickets:

```sh
klist
```

To renew your ticket (typically valid for 24 hours):

```sh
kinit -R
```

!!! note "Kerberos Troubleshooting"

    For advanced Kerberos configuration, token sharing across machines, and
    troubleshooting, see the [FAQs and Tips](../faqs_tips.md) page, which covers:

    - Sharing Kerberos tokens across multiple machines
    - Managing multiple Kerberos principals
    - Fixing common Kerberos issues

For more detailed information about SSH at CERN, see the
[CERN SSH FAQ](https://twiki.cern.ch/twiki/bin/view/LinuxSupport/SSHatCERNFAQ).
