---
title: "Install | secureblue"
description: "Steps to install secureblue"
permalink: /install
---

# Install

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Pre-install](#pre-install)
- [Terms of Use](#terms)
- [Installation](#installation)
  - [Secureblue ISO (Desktop)](#iso)
    - [ISO Verification](#verification)
  - [Ignition (CoreOS)](#ignition)
- [Post-install](#post-install)

<hr>

## [Pre-install](#pre-install)
{: #pre-install}

{% include alert.html type='note' content='The cross-platform Fedora Media Writer is the <em>official, tested, and supported</em> method for the creation of bootable media. Instructions are available in the <a href="https://docs.fedoraproject.org/en-US/fedora/latest/preparing-boot-media/">Fedora documentation</a>.' %}

Before installation, the following checks are recommended:

- Ensure SecureBoot is enabled.
- Ensure your BIOS is up-to-date by checking its manufacturer's website.
- Disable booting from USB (some manufacturers allow firmware changes from live systems).
- Set a BIOS password to prevent tampering.

<hr>

## [Terms of Use](#terms)
{: #terms}

secureblue includes a combination of software packages, each under its own licensing terms. The license of secureblue is the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). The license of secureblue does not supersede the licenses of upstream code and content contained in secureblue images. By downloading secureblue you agree to the license terms of its use.

```
Copyright 2024-2025 The Secureblue Authors

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

## [Installation](#installation)
{: #installation}

To install secureblue, you will use one of the following processes. Consult the table below for the right starting point for your use case. For more details on the available images, have a look at the <a href="/images">list of available images</a> before proceeding.

| Image Type | Installation Process                                                                           | Recommended Use Cases          |
|------------|------------------------------------------------------------------------------------------------|--------------------------------|
| Desktop    | Direct installation with a [secureblue ISO](#iso)                                              | Desktop/laptop end user        |
| Server     | Installation using [Ignition via Butane](#ignition).                                           | Cloud, containerized workloads |

Things to remember during installation:

- Select the option to encrypt the drive you're installing to.
- Use a [strong password](https://www.eff.org/dice) when prompted.
- Select wheel group membership for your user when prompted.

### [Secureblue ISO (Desktop)](#iso)
{: #iso}

{% include alert.html type='note' content='<b>nvidia-open</b> images are recommended for systems with NVIDIA GPUs Turing or newer (GTX 16XX+, RTX 20XX+). These include the new <a href="https://github.com/NVIDIA/open-gpu-kernel-modules">open kernel modules</a> from NVIDIA, not Nouveau. <b>nvidia</b> images are recommended for systems with NVIDIA GPUs Pascal or older. These include the closed kernel modules from NVIDIA.' %}

<form method="GET">
  <select name="de" id="de" required>
    <option value="">Select an environment</option>
    <option value="silverblue">GNOME</option>
    <option value="kinoite">KDE Plasma</option>
    <option value="sericea">Sway</option>
  </select>
  <select name="nvidia" id="nvidia" required>
    <option value="">Do you have NVIDIA?</option>
    <option value="main">No</option>
    <option value="nvidia">Yes (pre-Turing)</option>
    <option value="nvidia-open">Yes (Turing and later)</option>
  </select>
  <br><br>
  <label>
    <input type="checkbox" name="terms" required>
    I have read and agree to the <a href="#terms">Terms of Use</a>
  </label>
  <br><br>
  <button type="submit" formaction="https://isos.secureblue.dev/download">Download secureblue ISO</button>
  <button type="submit" formaction="https://isos.secureblue.dev/downloadSHA256SUM">Download ISO SHA256SUM</button>
  <a href="https://isos.secureblue.dev/secureblue-keyring.gpg" class="button">Download secureblue keyring</a>
</form>

#### [ISO Verification](#verification)
{: #verification}

Once you have the ISO, the CHECKSUM, and the keyring downloaded, use the following commands to verify the ISO (where `${IMAGE_NAME}` corresponds to the filename of the ISO you donwloaded):

First command:
```
gpgv --keyring ./secureblue-keyring.gpg "${IMAGE_NAME}.iso-CHECKSUM"
```

Expected output:
```
gpgv: Signature made Wed 04 Jun 2025 12:49:39 AM PDT
gpgv:                using EDDSA key 26B4463ED8F313BC7E3FBDF9D9223AF0F47B3E41
gpgv: Good signature from "secureblueadmin <secureblueadmin@proton.me>"
```

Second command:
```
sha256sum -c "${IMAGE_NAME}.iso-CHECKSUM"
```

Expected output:
```
IMAGE_NAME.iso: OK
sha256sum: WARNING: 8 lines are improperly formatted
```


### [Ignition (Server)](#ignition)
{: #ignition}

Follow the [Fedora CoreOS docs](https://docs.fedoraproject.org/en-US/fedora-coreos/getting-started/), [Ignition docs](https://docs.fedoraproject.org/en-US/fedora-coreos/producing-ign/), and [Butane docs](https://coreos.github.io/butane/) to configure initialization for your CoreOS instance(s).

You can use our [example.butane](https://github.com/secureblue/secureblue/blob/live/docs/example.butane) as a starting point.


<hr>

## [Post-install](#post-install)
{: #post-install}

- Essential
  - [Subscribe to secureblue release notifications](#release-notifications)
  - [Enroll SecureBoot key](#secureboot)
  - [Validation](#validation)
  - [Read the FAQ](#faq)
- Recommended
  - [Kernel argument tuning](#kargs)
  - [Setup USBGuard](#usbguard)
  - [Create a separate wheel account for admin purposes](#wheel)
  - [Setup system DNS](#dns)
  - [Toggle MAC address randomization](#mac-randomization)
  - [Bash environment lockdown](#bash)
  - [LUKS Hardware Unlock](#luks-hardware-unlock)
  - [Flatpak Permissions Tuning](#flatpak-permissions-tuning)
  - [Trivalent Flags](#trivalent-flags)

### [Subscribe to secureblue release notifications](#release-notifications)
{: #release-notifications}

Subscribing to release notifications is documented [here](/faq#releases).

### [Enroll SecureBoot key](#secureboot)
{: #secureboot}

{% include alert.html type='note' content='GNOME users on Nvidia images may notice that Gnome Software prompts them to create a new secureboot key. This prompt can and should be ignored, and the command below used instead.' %}

The secureblue Secure Boot key should automatically enroll after installation, with the MOK password "secureblue". If this fails or doesn't appear for whatever reason, you can manually enroll the key with the command below.

```
ujust enroll-secureblue-secure-boot-key
```

### [Validation](#validation)
{: #validation}

To validate your secureblue setup, run:

```
ujust audit-secureblue
```

### [Read the FAQ](#faq)
{: #faq}

Lots of important stuff is covered in the [FAQ](/faq). If you're having an issue, it's probably covered there already. AppImage toggles, GNOME extension toggles, Xwayland toggles, etc.

### [Kernel argument tuning](#kargs)
{: #kargs}

A stable set of kernel arguments is preinstalled with secureblue. However, it is recommended that you consult our [Kargs article](/articles/kargs) for guidance on tuning Kargs based on your use case.

### [Flatpak Permissions Tuning](#flatpak-permissions-tuning)
{: #flatpak-permissions-tuning}

Consult our [Flatpak article](/articles/flatpak) for guidance on tuning Flatpak permissions.

### [Setup USBGuard](#usbguard)
{: #usbguard}

This will generate a policy based on your currently attached USB devices and block all others, then enable usbguard.

```
ujust setup-usbguard
```

### [Create a separate wheel account for admin purposes](#wheel)
{: #wheel}

{% include alert.html type='caution' content='If you do these steps out of order, it is possible to end up without the ability to administrate your system. You will not be able to use the <a href="https://linuxconfig.org/recover-reset-forgotten-linux-root-password">traditional GRUB-based method</a> of fixing mistakes like this, either, as this will leave your system in a broken state. However, simply rolling back to an older snapshot of your system, should resolve the problem.' %}

Creating a dedicated wheel user and removing wheel from your primary user helps prevent certain privilege escalation attack vectors and password sniffing. We log in as admin to do the final step of removing the user account\'s wheel privileges in order to make the operation of removing those privileges depend on having access to your admin account, and the admin account functioning correctly first.' You don\'t need to log in using your wheel user to use it for privileged operations. When logged in as your non-wheel user, Polkit will prompt you to authenticate as your wheel user as needed, or when requested by calling <code>run0</code>.
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

### [Configure system DNS](#dns)
{: #dns}

The command below will interactively setup system DNS resolution for systemd-resolved (and optionally set the resolver for Trivalent via management policy). If you intend to use a VPN, use the system default state (network provided resolver). This will ensure your system uses the VPN provided DNS resolver to prevent DNS leaks. ESPECIALLY avoid setting the browser DNS policy in this case.

```
ujust dns-selector
```

### [Toggle MAC address randomization](#mac-randomization)
{: #mac-randomization }

Toggle system-wide MAC address randomization in NetworkManager between `random` and `permanent` using the command below. Disabling MAC randomization can help with network compatibility issues, especially in enterprise or captive portal environments. Enabling it improves privacy by preventing tracking across networks.

```
ujust toggle-mac-randomization
```

### [Bash environment lockdown](#bash)
{: #bash}

To mitigate [LD_PRELOAD attacks](https://github.com/Aishou/wayland-keylogger), run:

```
ujust toggle-bash-environment-lockdown
```

### [LUKS Hardware-Unlock](#luks-hardware-unlock)
{: #luks-hardware-unlock}

There are two options available for hardware-based unlocking. You can either enroll FIDO2 or TPM2 for your LUKS volume. FIDO2 enrollment is preferable if you own a hardware security key. It\'s recommended that you choose only one of these, and not both at the same time.


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

### [Trivalent Flags](#trivalent-flags)
{: #trivalent-flags}

The included [Trivalent](https://github.com/secureblue/Trivalent) browser has some additional settings in `chrome://flags` you may want to set for additional hardening and convenience (can cause functionality issues in some cases).

You can read about these settings in the [Trivalent post-install](https://github.com/secureblue/Trivalent?tab=readme-ov-file#post-install) instructions.
