# Signing Up for UChicago

## Account Registration

First you will need to sign up on the
[Analysis Facility website](https://af.uchicago.edu/).

You can use your institutional or CERN identity when signing up, the latter will
make the approval process smoother. Please enter your full name, your home
institution's name, and your institutional email; account requests from services
like Gmail, Outlook, iCloud, etc. won't be accepted.

!!! note "Not an ATLAS member yet?"

    If you are not yet an ATLAS member, but are working with someone in ATLAS,
    please
    [contact the UChicago facility team](../getting_help.md#facility-specific-support)
    explaining who you are working with and why you need an account.

---

## SSH Key Setup

Once your account is accepted, you will need to generate and upload an SSH key.

See our [SSH Access Guide](../computing/ssh_guide.md) for instructions on
generating SSH keys.

!!! warning "UChicago Key Requirements"

    The UChicago Analysis Facility will **not** accept deprecated key types such as
    DSA or RSA with a SHA-1 signature, as these are generally considered insecure.
    Please use modern elliptic curve keys (`ed25519` or `ecdsa`) as recommended in
    the SSH guide.

### Upload Your Public Key

After generating your SSH key pair (see the
[SSH guide](../computing/ssh_guide.md)), upload the public key to your profile
on the Analysis Facility portal by pasting its content into the "SSH public key"
text box.

You can view your public key with:

```sh
cat ~/.ssh/id_ed25519.pub
```

!!! danger "Important: Protect Your Private Key!"

    You must only upload the public key (.pub)! Treat the private key as if it were
    your password.

---

## First Login

Once you have uploaded your public key to your profile, it will take a little
while to synchronize with the system. After **~15 minutes**, you should be able
to login via SSH:

```sh
ssh login.af.uchicago.edu
```

If it does not work, please double check that you have been approved, have
uploaded your public key and have waited at least 15 minutes. If you still have
an issue, feel free to [reach out for help](../getting_help.md).

---

## Next Steps

Once you can successfully login, see the [Accessing UChicago](accessing.md)
guide for information on:

- SSH configuration options
- ATLAS environment setup
- Filesystem information
- Available tools and resources
