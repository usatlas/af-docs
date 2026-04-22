# Installation of Singularity

## Singularity on Linux OS

Most Linux Distros should come with the Singularity. For example, Singularity is
already available on BNL/SLAC machines.

    cent7a(SLAC)$ singularity --version
    singularity version 3.5.3-1.1.el7

    attsub01(BNL)$ singularity --version
    2.6.1-dist

If it does not come with your Linux OS, you can find the installation
instruction for the version of 3.5 (the latest one currently):

<https://sylabs.io/guides/3.5/admin-guide/installation.html>

The singularity maintained in Linux distribution repos (via apt or yum) tends to
be older. If you like to install the latest version, you can visit
[the gitlab source site](https://github.com/sylabs/singularity/releases) to
install from the source.

## Singularity on Mac OS

Since Mac OS does not use Linux kernel, the Singularity for Linux does not work
here. However, a new Singularity Desktop for Mac OS has been developed to take
Linux-based containers. The installation instruction can be found
[here](https://sylabs.io/singularity-desktop-macos/). It is still a beta
release, and distributed as a DMG file (Mac OS disk image). The current beta
release version is:

    MacOS$ singularity --version
    singularity version 3.3.0-rc.1.658.g7427b73f1.dirty

As stated on the page of
[Singularity Desktop MacOS](https://sylabs.io/singularity-desktop-macos/), there
are some limitations.

Run **singularity -h** to find the full available commands and options. In
comparison with the Singularity-3.5 on Linux, the Singularity on Mac OS misses
many commands such as **inspect** and **instance**.

## Singularity on Windows

In order to use Singularity on Windows, you need install a Linux distro first.
It could be achieved through
[Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/faq)
without involving a Virtual Machine. **WSL** is a new Windows 10 feature that
enables you to run native Linux command-line tools directly on Windows. So it is
not available for other old Windows such as Windows 7.

### Installation of Windows Subsystem for Linux (WSL)

Please refer
[the WSL installation guide for Windows 10/11](https://learn.microsoft.com/en-us/windows/wsl/install).

First enable the option feature **Microsoft-Windows-Subsystem-Linux**. Open
**PowerShell as Administrator** and run:

    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux

When prompted restart the computer.

Then you can install your preferred Linux Distro, following
[the link on the Microsoft store](https://docs.microsoft.com/en-us/windows/wsl/install-win10),
where there is no CentOS available. However, you can find the installation guide
for CentOS at
[TipsMake.com](https://tipsmake.com/install-centos-on-windows-10-wsl). Or
download directly [the zip file at github](https://github.com/yuk7/CentWSL) and
following the instruction to install it.

Once your distro has been downloaded and installed, you will be prompted to
**create a new user account** together with its password to
[initialize the new Linux distro](https://learn.microsoft.com/en-us/windows/wsl/build-custom-distro).

### Running Windows 10 build 19041 or higher or Windows 11

In order to install WSL2, ensure that you must be running Windows 10...

- For x64 systems: Version 1903 or later, with Build 18362.1049 or later.
- For ARM64 systems: Version 2004 or later, with Build 19041 or later.

or Windows 11.

You can check your Windows version by opening **Command Prompt** and running the
**ver** command.

>     Microsoft Windows [Version 10.0.19045.3803]
>     (c) 2020 Microsoft Corporation. All rights reserved.
>
>     C:\Users\Shuwei>ver
>
>     Microsoft Windows [Version 10.0.19045.3803]

Actually the Windows build information has already be displayed on the terminal
top when the **Command Prompt** app is opened.

You can also check the Windows build info in PowerShell with command
**systeminfo**:

>     PS C:\Users\Shuwei> systeminfo | Select-String "^OS Name","^OS Version"
>
>     OS Name:                   Microsoft Windows 10 Home Insider Preview
>     OS Version:                10.0.19045 N/A Build 19045

If your Windows build is lower than **18362** (for x64 systems) or 19041 (for
ARM64 systems), you can use the
[Windows Update Assistant](https://www.microsoft.com/software-download/windows10)
to update your version of Windows.

### Installation of WSL2

You can find
[the detailed instruction on installing WSL2 on Windows](https://learn.microsoft.com/en-us/windows/wsl/install).
The first 2 requirements have already been discussed above. Next you need:

- Enable the 'Virtual Machine Platform' optional component
- Set a distro to be backed by WSL2 using the command line
- Verify what versions of WSL your distros are using

To Enable the **Virtual Machine Platform**, run **PowerShell as Administrator**
with:

    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

Then restart the computer.

Next set the Linux dsitro to WSL2 by the following command under **PowerShell**:

    wsl --set-default-version 2

which may take a while to apply.

Now you can verify which WSL version used the Linux distro.

>     PS C:\Users\Shuwei> wsl -v
>     WSL version: 2.0.9.0
>     Kernel version: 5.15.133.1-1
>     WSLg version: 1.0.59
>     MSRDC version: 1.2.4677
>     Direct3D version: 1.611.1-81528511
>     DXCore version: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
>     Windows version: 10.0.19045.3803

### Linux Distribution Installation on Windows

Running `wsl -l --online` in either **PowerShell** or **Command Prompt** to list
the available Linux Disibution Systems.

>     PS C:\Users\yesw> wsl -l --online
>     The following is a list of valid distributions that can be installed.
>     Install using 'wsl.exe --install <Distro>'.
>
>     NAME                                   FRIENDLY NAME
>     Ubuntu                                 Ubuntu
>     Debian                                 Debian GNU/Linux
>     kali-linux                             Kali Linux Rolling
>     Ubuntu-18.04                           Ubuntu 18.04 LTS
>     Ubuntu-20.04                           Ubuntu 20.04 LTS
>     Ubuntu-22.04                           Ubuntu 22.04 LTS
>     OracleLinux_7_9                        Oracle Linux 7.9
>     OracleLinux_8_7                        Oracle Linux 8.7
>     OracleLinux_9_1                        Oracle Linux 9.1
>     openSUSE-Leap-15.5                     openSUSE Leap 15.5
>     SUSE-Linux-Enterprise-Server-15-SP4    SUSE Linux Enterprise Server 15 SP4
>     SUSE-Linux-Enterprise-15-SP5           SUSE Linux Enterprise 15 SP5
>     openSUSE-Tumbleweed                    openSUSE Tumbleweed

There are Linux Distribution Systems for WSL2 available in the
[Microsoft Store](https://apps.microsoft.com), such as _AlmaLinux_.

To install _Ubuntu_, just run `wsl --install Ubuntu`.

To install _AlmaLinux9_, find the corresponding app in the **Microsoft Store**,
install it.

#### Nameservers in WSL2 Linux

In case the Windows machine is **behind a campus firewall**, the automatically
generated file _/etc/resolv.conf_ would not work properly. In the case, you need
override the file _/etc/resolv.conf_, which is actually a sym-link to
_/mnt/wsl/resolv.conf_ by default.

Step-1: Inside the WSL2 Linux, run `ipconfig.exe /all | grep -A1 "DNS Servers"`
to find the nameservrs on the host.

>     AlmaLinux9$ ipconfig.exe /all | grep -A1 "DNS Servers"
>     DNS Servers . . . . . . . . . . . : 130.199.128.31
>                                         130.199.1.1
>     --
>     DNS Servers . . . . . . . . . . . : fec0:0:0:ffff::1%1
>                                         fec0:0:0:ffff::2%1

Step-2: Remove the sym-linked file _/etc/resolv.conf_, then create a new file
with **the above IPv4 DNS servers**.

>     AlmaLinux9$ sudo rm -f /etc/resolv.conf
>     AlmaLinux9$ sudo cat >/etc/resolv.conf
>     nameserver 130.199.128.31
>     nameserver  130.199.1.1

Step-3: Create a new file _/etc/wsl.conf_ with the following content, to prevent
overrding _/etc/resolv.conf_ during WSL2 restart.

>     AlmaLinux9$ sudo cat > /etc/wsl.conf
>     [network]
>     generateResolvConf = false

### Use the Installed Linux on Windows

Open **PowerShell under a regular user** and run **wsl**:

>     PS C:\Users\Shuwei> wsl
>     yesw2000@Home-Dell660:/mnt/c/Users/Shuwei$ echo $0
>     -bash
>     yesw2000@Home-Dell660:/mnt/c/Users/Shuwei$

which starts the Linux and enter **bash**.

You can also start the Linux by searching **wsl** or **bash** on the Windows
Start Search Box and click on **wsl Run command** or **bash Run command**.

If the Linux is already running, you can run "bash" to enter the Linux:

>     PS C:\Users\Shuwei> wsl -l -v
>       NAME            STATE           VERSION
>     * Ubuntu-18.04    Running         2
>     PS C:\Users\Shuwei> bash
>     yesw2000@Home-Dell660:/mnt/c/Users/Shuwei$

After all terminals associated with the Linux have been closed, the running
Linux will stop then.

### Singularity Installation on WSL2

Start the Linux distro on Windows, then install Singularity as the same ways as
on the Linux OS.
