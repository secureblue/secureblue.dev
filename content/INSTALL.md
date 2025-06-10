---
title: "Install | secureblue"
description: "Steps to install secureblue"
permalink: /install
---

# Install

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Pre-install](#pre-install)
  - [BIOS hardening](#bios-hardening)
- [Terms of use](#terms)
- [Installation](#installation)
  - [Secureblue ISO (Desktop)](#iso)
  - [Ignition (CoreOS)](#ignition)
- [Post-install](#post-install)

<hr>

## [Pre-install](#pre-install)
{: #pre-install}

{% include alert.html type='note' content='The cross-platform Fedora Media Writer is the <em>official, tested, and supported</em> method for the creation of bootable media. Instructions (alongside a word on alternative methods) are available in the <a href="https://docs.fedoraproject.org/en-US/fedora/latest/preparing-boot-media/">Fedora documentation</a>.' %}

Before installation, the following checks are recommended:

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

## Installation
{: #installation}

To install secureblue, you will use one of the following processes. Consult the table below for the right starting point for your use case.

| Image Type | Installation Process                                                                           | Recommended Use Cases          |
|------------|------------------------------------------------------------------------------------------------|--------------------------------|
| Desktop    | Direct installation with a [secureblue ISO]()                                                  | Desktop/laptop end user        |
| CoreOS     | Installation using [Ignition via Butane](#ignition).                                           | Cloud, containerized workloads |
| IoT        | Installation with a Fedora IoT ISO, followed by [rebasing](#rebase) to secureblue.             | IoT / bare-metal servers       |

Things to remember during installation:

- Select the option to encrypt the drive you're installing to.
- Use a [strong password](https://security.harvard.edu/use-strong-passwords) when prompted.
- Leave the root account disabled if prompted.
- Select wheel group membership for your user if prompted.

### [Secureblue ISO (Desktop)](#iso)
{: #iso}

<form method="GET">
  <select name="de" id="de" required>
    <option value="">Select an environment</option>
    <option value="silverblue">GNOME</option>
    <option value="kinoite">KDE Plasma</option>
    <option value="sericea">Sway</option>
    <option value="cosmic">COSMIC</option>
  </select>
  <br>
  <select name="nvidia" id="nvidia" required>
    <option value="">Do you have NVIDIA?</option>
    <option value="main">No</option>
    <option value="nvidia">Yes (pre-Turing)</option>
    <option value="nvidia-open">Yes (Turing and later)</option>
  </select>
  <br>
  <button type="submit" formaction="https://isos.secureblue.dev/download">Download secureblue ISO</button>
  <br>
  <button type="submit" formaction="https://isos.secureblue.dev/downloadSHA256SUM">Download ISO SHA256SUM</button>
  <br>
  <button type="submit" formaction="https://isos.secureblue.dev/secureblue-keyring.gpg">Download secureblue keyring</button>
</form>

Once you have the ISO, the CHECKSUM, and the keyring downloaded, use the following commands to verify the ISO (where `${IMAGE_NAME}` corresponds to the filename of the ISO you donwloaded):

First command:
```
gpgv --keyring ./secureblue-keyring.gpg "${IMAGE_NAME}.iso-CHECKSUM"
```

Second command:
```
sha256sum --ignore-missing -c "${IMAGE_NAME}.iso-CHECKSUM"
```


Expected output from the first command:
```
gpgv: Signature made Wed 04 Jun 2025 12:49:39 AM PDT
gpgv:                using EDDSA key 26B4463ED8F313BC7E3FBDF9D9223AF0F47B3E41
gpgv: Good signature from "secureblueadmin <secureblueadmin@proton.me>"
```


### [Ignition (CoreOS)](#ignition)
{: #ignition}

Follow the [Fedora CoreOS docs](https://docs.fedoraproject.org/en-US/fedora-coreos/getting-started/), [Ignition docs](https://docs.fedoraproject.org/en-US/fedora-coreos/producing-ign/), and [Butane docs](https://coreos.github.io/butane/) to configure initialization for your CoreOS instance(s).

You can use our [example.butane](https://github.com/secureblue/secureblue/blob/live/docs/example.butane) as a starting point.


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
- [Toggle MAC address randomization](#mac-randomization)
- [Bash environment lockdown](#bash)
- [LUKS Hardware Unlock](#luks-hardware-unlock)
- [Flatpak Permissions Tuning](#flatpak-permissions-tuning)
- [Validation](#validation)
- [Optional: Trivalent Flags](#trivalent-flags)
- [Read the FAQ](#faq)

### [Subscribe to secureblue release notifications](#release-notifications)
{: #release-notifications}

[How to subscribe to secureblue release notifications](/faq#releases)

### [Set NVIDIA-specific kargs if applicable](#nvidia)
{: #nvidia}

If you are using an `nvidia` or `nvidia-open` image, run this after installation:

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

{% include alert.html type='note' content='GNOME users on Nvidia images may notice that Gnome Software prompts them to create a new secureboot key. This prompt can and should be ignored, and the command below used instead.' %}

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

### [Toggle MAC address randomization](#mac-randomization)
{: #mac-randomization }

Toggle system-wide MAC address randomization in NetworkManager between `random` and `permanent`:

```
ujust toggle-mac-randomization
```

{% include alert.html type='note' content='Disabling MAC randomization can help with network compatibility issues, especially in enterprise or captive portal environments. Enabling it improves privacy by preventing tracking across networks.' %}

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

{% include alert.html type='warning' content='If you have an AMD CPU, check your firmware settings to make sure it is using a dedicated TPM device or a Pluton Chip. If not and it is using an fTPM (firmware TPM), skip this step. If you do not know what this means or are unsure, just skip this step.' %}

To enable TPM2 LUKS unlocking, run:

```
ujust setup-luks-tpm-unlock
```

Type `Y` when asked if you want to set a PIN.

### [Flatpak Permissions Tuning](#flatpak-permissions-tuning)

Consult our [Flatpak article](/articles/flatpak) for guidance on tuning Flatpak permissions.

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
