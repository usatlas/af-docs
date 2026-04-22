# Accessing UChicago

This guide covers accessing the UChicago Analysis Facility via SSH, including
SSH configuration, filesystems, and setting up the ATLAS environment.

!!! note "Don't have an account yet?"

    If you haven't signed up for a UChicago Analysis Facility account yet, please
    see [Signing Up for UChicago](account.md) first.

---

## SSH Connection

Once your account is approved and your SSH key is synchronized with the system
(~15 minutes after upload), you can connect to the facility:

```sh
ssh login.af.uchicago.edu
```

For detailed information on SSH key generation and setup, see our
[SSH Access Guide](../computing/ssh_guide.md).

!!! tip "Need help connecting?"

    See our [Getting Help](../getting_help.md) page for support options and how to
    reach the ATLAS AF team.

---

## SSH Configuration (Optional)

For detailed SSH configuration options including config files and SSH agent
setup, see our [SSH Access Guide](../computing/ssh_guide.md).

### UChicago-Specific SSH Config Example

You can create an SSH config file entry specifically for UChicago to simplify
connections:

```sh
Host uchicago
  HostName login.af.uchicago.edu
  User <your-username>
  ForwardAgent yes
  IdentityFile ~/.ssh/id_ed25519
```

Replace `<your-username>` with your actual username. With this configuration,
you can simply run:

```sh
ssh uchicago
```

For more information on SSH config files and adding keys to the SSH agent, see
the [SSH Access Guide](../computing/ssh_guide.md#ssh-configuration-optional)
