---
title: "Install | secureblue"
description: "Steps to install secureblue"
permalink: /install
---

# Install

To install secureblue, you will use a Fedora Atomic (or CoreOS, for securecore) ISO to install Fedora Atomic, then rebase to a secureblue image using the installer. Unless specified otherwise, secureblue is used to refer to both the secureblue set of images and the securecore set of images, for the sake of brevity. The installation script presented in a later step lets you choose between them. You *must* start from a Fedora Atomic ISO for secureblue desktop images, and *must* start from a Fedora CoreOS ISO for securecore images.

## [Table of Contents](#table-of-contents)
{: #table-of-contents}
- [Pre-install](#pre-install)
  - [Fedora installation](#fedora-installation)
  - [BIOS hardening](#bios-hardening)
- [Terms of use](#terms)
- [Rebase](#rebase)
- [Post-install](#post-install)

<hr>

## [Pre-install](#pre-install)
{: #pre-install}

The following is advice on what to do before and during the installation of a Fedora ISO, and how.

{% include alert.html type='note' content='The cross-platform Fedora Media Writer is the <em>official, tested, and supported</em> method for the creation of bootable media. Instructions (alongside a word on alternative methods) are available in the <a href="https://docs.fedoraproject.org/en-US/fedora/latest/preparing-boot-media/">Fedora documentation</a>.' %}

{% include alert.html type='tip' content='If you don\'t already have a Fedora Atomic installation, use a Fedora Atomic ISO that matches your secureblue target image to install one. If you want to use a secureblue Silverblue image, start with the Fedora Silverblue ISO, Kinoite for Kinoite, Sericea (Sway Atomic) for Sericea and all the Wayblue images, and CoreOS for all the securecore images.<br>For more details on the available images, have a look at the <a href="/images">list of available images</a> before proceeding.' %}

{% include alert.html type='caution' content='The Fedora 41 ISO contains a bugged version of rpm-ostree. As such, after using it to install Fedora Atomic, you <em>must</em> run rpm-ostree upgrade and then restart, before running the secureblue installer.' %}

Before rebasing and during the installation, the following checks are recommended.

### [Fedora installation](#fedora-installation)
{: #fedora-installation}

- Select the option to encrypt the drive you're installing to.
- Use a [strong password](https://security.harvard.edu/use-strong-passwords) when prompted.
- Leave the root account disabled if prompted.
- Select wheel group membership for your user if prompted.

### [BIOS hardening](#bios-hardening)
{: #bios-hardening}
- Ensure SecureBoot is enabled.
- Ensure your BIOS is up-to-date by checking its manufacturer's website.
- Disable booting from USB (some manufacturers allow firmware changes from live systems).
- Set a BIOS password to prevent tampering.

<hr>

## [Terms of use](#terms)
{: #terms}

secureblue includes a combination of software packages, each under its own licensing terms. The license of secureblue is the Apache License 2.0. The license of secureblue does not supersede the licenses of upstream code and content contained in secureblue images. By downloading secureblue you agree to the license terms of its use.

```
Copyright 2024-2025 The secureblue authors

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this software except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

<hr>

## [Rebase](#rebase)
{: #rebase}

Now that you have a Fedora Atomic or Fedora CoreOS installation, rebase it to the secureblue image of your choice using the script below. This script does not install secureblue into the existing system. It rebases (fully replaces the existing system) with secureblue.


<a class="button" href="https://github.com/secureblue/secureblue/releases/latest/download/install_secureblue.sh">Download secureblue installer</a>

After downloading the installer, run it from the directory you downloaded it to:

```
bash install_secureblue.sh
```

<hr>

## [Post-install](#post-install)
{: #post-install}

- [Subscribe to secureblue release notifications](#release-notifications)
- [Set NVIDIA-specific kargs if applicable](#nvidia)
- [Enroll SecureBoot key](#secureboot)
- [Set hardened kargs](#kargs)
  - [32-bit support](#kargs-32-bit)
  - [Force disable simultaneous multithreading](#kargs-smt)
  - [Unstable hardening kargs](#kargs-unstable)
- [Setup USBGuard](#usbguard)
- [Create a separate wheel account for admin purposes](#wheel)
- [Setup system DNS](#dns)
- [Bash environment lockdown](#bash)
- [LUKS Hardware Unlock](#luks-hardware-unlock)
- [Validation](#validation)
- [Optional: Trivalent Flags](#trivalent-flags)
- [Read the FAQ](#faq)

{% include alert.html type='note' content='After installation, <a href="https://github.com/ublue-os/yafti">yafti</a> will open. Make sure to follow the steps listed carefully and read the directions closely.' %}

### [Subscribe to secureblue release notifications](#release-notifications)
{: #release-notifications}

[How to subscribe to secureblue release notifications](/faq#releases)

### [Set NVIDIA-specific kargs if applicable](#nvidia)
{: #nvidia}

If you are using an `nvidia` image, run this after installation:

```
ujust set-kargs-nvidia
```

If you encounter flickering or LUKS issues, you may also (rarely) need this karg:

```
rpm-ostree kargs \
    --append-if-missing=initcall_blacklist=simpledrm_platform_driver_init
```

### [Enroll SecureBoot key](#secureboot)
{: #secureboot}

```
ujust enroll-secureblue-secure-boot-key
```

### [Set hardened kargs](#kargs)
{: #kargs}

{% include alert.html type='note' content='Learn more about the <a href="/articles/kargs">hardened boot kargs</a> applied by the command below.' %}

```
ujust set-kargs-hardening
```

This command applies a fixed set of hardened boot parameters, and asks you whether the following kargs should *also* be set along with those (all of which are documented in the link above):

#### [32-bit support](#kargs-32-bit)
{: #kargs-32-bit}

If you answer `N`, or press enter without any input, support for 32-bit programs will be disabled on the next boot. If you run exclusively modern software, chances are likely you don't need this, so it's safe to disable for additional attack surface reduction.

However, there are certain exceptions. A couple common usecases are if you require Steam, or run an occasional application in Wine you'll likely want to keep support for 32-bit programs. If this is the case, answer `Y`.

#### [Force disable simultaneous multithreading](#kargs-smt)
{: #kargs-smt}

If you answer `Y` when prompted, simultaneous multithreading (SMT, often called Hyper-threading) will be forcefully disabled, regardless of known vulnerabilities in the running hardware. This can cause a reduction in the performance of certain tasks in favor of security.

#### [Unstable hardening kargs](#kargs-unstable)
{: #kargs-unstable}

If you answer `Y` when prompted, unstable hardening kargs will be additionally applied, which can cause issues on some hardware, but are stable on other hardware.

### [Setup USBGuard](#usbguard)
{: #usbguard}

*This will generate a policy based on your currently attached USB devices and block all others, then enable usbguard.*

```
ujust setup-usbguard
```

### [Create a separate wheel account for admin purposes](#wheel)
{: #wheel}

Creating a dedicated wheel user and removing wheel from your primary user helps prevent certain privilege escalation attack vectors and password sniffing.

{% include alert.html type='caution' content='If you do these steps out of order, it is possible to end up without the ability to administrate your system. You will not be able to use the <a href="https://linuxconfig.org/recover-reset-forgotten-linux-root-password">traditional GRUB-based method</a> of fixing mistakes like this, either, as this will leave your system in a broken state. However, simply rolling back to an older snapshot of your system, should resolve the problem.' %}

{% include alert.html type='note' content='We log in as admin to do the final step of removing the user account\'s wheel privileges in order to make the operation of removing those privileges depend on having access to your admin account, and the admin account functioning correctly first.' %}
1. `run0`
2. `adduser admin`
3. `usermod -aG wheel admin`
4. `passwd admin`
5. `exit`
6. `reboot`
7. Log in as `admin`
8. `run0`
9. `gpasswd -d {your username here} wheel`
10. `reboot`

{% include alert.html type='note' content='You don\'t need to log in using your wheel user to use it for privileged operations. When logged in as your non-wheel user, Polkit will prompt you to authenticate as your wheel user as needed, or when requested by calling <code>run0</code>.' %}

### [Setup system DNS](#dns)
{: #dns}

Interactively setup system DNS resolution for systemd-resolved (optionally also set the resolver for Trivalent via management policy):

```
ujust dns-selector
```

{% include alert.html type='note' content='If you intend to use a VPN, use the system default state (network provided resolver). This will ensure your system uses the VPN provided DNS resolver to prevent DNS leaks. ESPECIALLY avoid setting the browser DNS policy in this case.' %}

### [Bash environment lockdown](#bash)
{: #bash}

To mitigate [LD_PRELOAD attacks](https://github.com/Aishou/wayland-keylogger), run:

```
ujust toggle-bash-environment-lockdown
```

### [LUKS Hardware-Unlock](#luks-hardware-unlock)
{: #luks-hardware-unlock}

{% include alert.html type='note' content='There are two options available for hardware-based unlocking. You can either enroll FIDO2 or TPM2 for your LUKS volume. FIDO2 enrollment is preferable if you own a hardware security key. It\'s recommended that you choose only one of these, and not both at the same time.' %}


#### [LUKS FIDO2 Unlock](#luks-fido2)
{: #luks-fido2}

To enable FIDO2 LUKS unlocking with your FIDO2 security key, run:

```
ujust setup-luks-fido2-unlock
```

#### [LUKS TPM2 Unlock](#luks-tpm2)
{: #luks-tpm2}

{% include alert.html type='warning' content='Do not use this if you have an AMD CPU.' %}

To enable TPM2 LUKS unlocking, run:

```
ujust setup-luks-tpm-unlock
```

Type `Y` when asked if you want to set a PIN.

### [Validation](#validation)
{: #validation}

To validate your secureblue setup, run:

```
ujust audit-secureblue
```

### [Optional: Trivalent Flags](#trivalent-flags)
{: #trivalent-flags}

The included [Trivalent](https://github.com/secureblue/Trivalent) browser has some additional settings in `chrome://flags` you may want to set for additional hardening and convenience (can cause functionality issues in some cases).

You can read about these settings in the [Trivalent post-install](https://github.com/secureblue/Trivalent?tab=readme-ov-file#post-install) instructions.

### [Read the FAQ](#faq)
{: #faq}

Lots of important stuff is covered in the [FAQ](/faq). AppImage toggles, GNOME extension toggles, Xwayland toggles, etc.
