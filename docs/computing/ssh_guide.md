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

To avoid entering your passphrase repeatedly, add your key to the SSH agent:

```sh
ssh-add ~/.ssh/id_ed25519
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
