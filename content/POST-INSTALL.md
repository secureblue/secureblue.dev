---
title: "Post-install | secureblue"
description: "Steps to complete your secureblue installation"
permalink: /post-install
---

# Post-install

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- Essential
  - [Subscribe to secureblue release notifications](#release-notifications)
  - [Enroll Secure Boot key](#secureboot)
  - [Validation](#validation)
  - [Read the FAQ](#faq)
- Recommended
  - [Kernel argument tuning](#kargs)
  - [Disable booting from USB](#booting-from-usb)
  - [Setup USBGuard](#usbguard)
  - [Create a separate wheel account for admin purposes](#wheel)
  - [Configure system DNS](#dns)
  - [Toggle MAC address randomization](#mac-randomization)
  - [Bash environment lockdown](#bash)
  - [LUKS Hardware Unlock](#luks-hardware-unlock)
  - [Flatpak Permissions Tuning](#flatpak-permissions-tuning)
  - [Trivalent Flags](#trivalent-flags)

<hr>

## [Subscribe to secureblue release notifications](#release-notifications)
{: #release-notifications}

Subscribing to release notifications is documented [here](/faq#releases).

## [Enroll Secure Boot key](#secureboot)
{: #secureboot}

{% include alert.html type='note' content='GNOME users on Nvidia images may notice that Gnome Software prompts them to create a new Secure Boot key. This prompt can and should be ignored, and the command below used instead. Nvidia users should expect Nvidia drivers not to load unless Secure Boot is enabled and the secureblue key enrolled.' %}

The secureblue Secure Boot key should automatically enroll after installation, with the MOK password "secureblue". If this fails or doesn't appear for whatever reason, you can manually enroll the key with the command below.

```
ujust enroll-secureblue-secure-boot-key
```

## [Validation](#validation)
{: #validation}

To validate your secureblue setup, run:

```
ujust audit-secureblue
```

## [Read the FAQ](#faq)
{: #faq}

A lot of technical issues are covered in the [FAQ](/faq). For new users, the following topics are particularly important to read:

- [Why is Bluetooth disabled? How do I enable it?](/faq#bluetooth)
- [Why doesn’t my Xwayland app work?](/faq#xwayland)
- [An app I use won’t start due to a malloc issue. How do I fix it?](/faq#standard-malloc)
- [Why don’t my appimages work?](/faq#appimage)
- [How do I install my VPN?](/faq#vpn)
- [Why am I unable to start containers?](/faq#container-userns)

## [Kernel argument tuning](#kargs)
{: #kargs}

A stable set of kernel arguments is preinstalled with secureblue. However, it is recommended that you consult our [Kargs article](/articles/kargs) for guidance on tuning Kargs based on your use case.

## [Flatpak Permissions Tuning](#flatpak-permissions-tuning)
{: #flatpak-permissions-tuning}

Consult our [Flatpak article](/articles/flatpak) for guidance on tuning Flatpak permissions.

## [Disable booting from USB](#booting-from-usb)
{: #booting-from-usb}

Some manufacturers allow firmware changes from live systems.

To access your device's BIOS/UEFI screen to disable booting from USB, you can run:

```
ujust bios
```

## [Setup USBGuard](#usbguard)
{: #usbguard}

This will generate a policy based on your currently attached USB devices and block all others, then enable usbguard.

```
ujust setup-usbguard
```

## [Create a separate wheel account for admin purposes](#wheel)
{: #wheel}

Creating a dedicated wheel user and removing wheel from your primary user helps prevent certain privilege escalation attack vectors and password sniffing. You don't need to log in using your wheel user to use it for privileged operations. When logged in as your non-wheel user, Polkit will prompt you to authenticate as your wheel user as needed, or when requested by calling `run0`.

Running the command below will automatically setup an admin account and ask you to select a password for it.

```
ujust create-admin
```

## [Configure system DNS](#dns)
{: #dns}

The command below will interactively set up system DNS resolution for Unbound, and optionally set the resolver for Trivalent via management policy. Choose **Configure global DNS.**

```
ujust dns-selector
```

{% include alert.html type='warning' content='If you intend to use a VPN, use the system default DNS. You may also have to use systemd&#8209;resolved with <code>ujust dns-selector resolver resolved</code>. This will ensure your system uses the VPN provided resolver to prevent DNS leaks. Especially avoid setting the Trivalent DNS over HTTPS policy in this case.' %}

## [Toggle MAC address randomization](#mac-randomization)
{: #mac-randomization }

Toggle system-wide MAC address randomization in NetworkManager between `random` and `permanent` using the command below. Disabling MAC randomization can help with network compatibility issues, especially in enterprise or captive portal environments. Enabling it improves privacy by preventing tracking across networks.

```
ujust toggle-mac-randomization
```

## [Bash environment lockdown](#bash)
{: #bash}

To mitigate [LD_PRELOAD attacks](https://github.com/Aishou/wayland-keylogger), run:

```
ujust toggle-bash-environment-lockdown
```

## [LUKS Hardware-Unlock](#luks-hardware-unlock)
{: #luks-hardware-unlock}

There are two options available for hardware-based unlocking. You can either enroll FIDO2 or TPM2 for your LUKS volume. FIDO2 enrollment is preferable if you own a hardware security key. It\'s recommended that you choose only one of these, and not both at the same time.

### [LUKS FIDO2 Unlock](#luks-fido2)
{: #luks-fido2}

To enable FIDO2 LUKS unlocking with your FIDO2 security key, run:

```
ujust setup-luks-fido2-unlock
```

### [LUKS TPM2 Unlock](#luks-tpm2)
{: #luks-tpm2}

{% include alert.html type='warning' content='If you have an AMD CPU, check your firmware settings to make sure it is using a dedicated TPM device or a Pluton Chip. If not and it is using an fTPM (firmware TPM), skip this step. If you do not know what this means or are unsure, just skip this step.' %}

To enable TPM2 LUKS unlocking, run:

```
ujust setup-luks-tpm-unlock
```

Type `Y` when asked if you want to set a PIN.

## [Trivalent Flags](#trivalent-flags)
{: #trivalent-flags}

The included [Trivalent](https://github.com/secureblue/Trivalent) browser has some additional settings in `chrome://flags` you may want to set for additional hardening and convenience (can cause functionality issues in some cases).

You can read about these settings in the [Trivalent post-install](https://github.com/secureblue/Trivalent?tab=readme-ov-file#post-install) instructions.
