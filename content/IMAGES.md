---
title: "Images | secureblue"
description: "List of available secureblue hardened operating system images"
permalink: /images
---

# Images

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Security recommendation](#security-recommendation)
- [Desktop](#desktop)
  - [Stable](#stable)
  - [Experimental](#experimental)
- [Server](#server)
  - [CoreOS](#coreos)
  - [IoT](#iot)

## [Security recommendation](#security-recommendation)
{: #security-recommendation}

GNOME, KDE Plasma, Sway, and COSMIC (Silverblue, Kinoite, Sericea, and COSMIC images, respectively) secure privileged Wayland protocols like screencopy. This means that on environments outside of GNOME, KDE Plasma, Sway, and COSMIC, applications can access screen content of the entire desktop. This implicitly includes the content of other applications. It\'s primarily for this reason that Silverblue, Kinoite, and Sericea images are recommended.

In addition, GNOME also provides weak <a href="https://gitlab.gnome.org/GNOME/gnome-desktop/-/issues/213">thumbnailer sandboxing</a> in Gnome Files, and Thunar/Tumblerd on secureblue Sway images provide weak [thumbnailer sandboxing](https://gitlab.gnome.org/GNOME/glycin/-/blob/main/glycin/src/sandbox.rs#L32) via Glycin. These are both efforts to mitigate <a href="https://scarybeastsecurity.blogspot.com/2016/11/0day-exploit-compromising-linux-desktop.html">attacks via thumbnailers</a>. COSMIC [is planning](https://github.com/pop-os/cosmic-files/issues/1189#event-20127287968) to add thumbnailer sandboxing for the release of Epoch 2. It's not known whether KDE plans to add this to Dolphin.

It should also be noted that our Sericea images disable the wlroots desktop portal, despite it being commonly used alongside Sway. This is because the portal reintroduces the screencopy vulnerability described above, which would undermine the security improvements in Sway for sandboxed applications. The downside of this is that by default on our Sericea images, flatpaks and applications that haven't implemented protocol support (like chromium-based browsers) are entirely prevented from screenshotting and screensharing. If necessary, Sway users can configure this using their own <a href="https://flatpak.github.io/xdg-desktop-portal/docs/portals.conf.html">portals.conf</a>.

This section is a relative recommendation between the desktop environments available on secureblue. This should not be misconstrued as saying that any one solves any of the fundamental issues with desktop Linux security. For more details, consult the table below. Note: this table assumes no extensions are installed. Extensions are a significant source of attack surface in and of themselves and they can reduce the effectiveness of, or completely negate, the security advantages described above.

| DE/WM      | Secures privileged Wayland protocols? | Thumbnailer sandboxing? | Stability    | Recommendation                  |
|------------|---------------------------------------|-------------------------|--------------|---------------------------------|
| GNOME      | Yes                                   | Weak                    | Stable       | Recommended                     |
| KDE Plasma | Yes                                   | None                    | Stable       | Recommended                     |
| Sway       | Yes                                   | Weak                    | Stable       | Recommended for tiling WM users |
| COSMIC     | Yes                                   | None                    | Beta         | Not currently recommended       |

{% include alert.html type='note' content='<code>nvidia-open</code> images are recommended for systems with NVIDIA GPUs Turing or newer (GTX 16XX+, RTX 20XX+). These include NVIDIA's proprietary drivers with their new <a href="https://github.com/NVIDIA/open-gpu-kernel-modules">open source kernel modules</a>, not to be confused with the reverse engineered open source Nouveau drivers (available in the <code>main</code> images). <code>nvidia</code> images are recommended for systems with NVIDIA GPUs Pascal or older. These include the closed source kernel modules from NVIDIA. Consult <a href="https://nouveau.freedesktop.org/CodeNames.html">this page</a> if you&#39;re not sure what family your GPU belongs to.' %}


## [Desktop](#desktop)
{: #desktop}

### [Stable](#stable)
{: #stable}

#### Silverblue (GNOME)

| Name                              | Base       | NVIDIA Support                                                                               | ARM64 Support |
|-----------------------------------|------------|----------------------------------------------------------------------------------------------|---------------|
| `silverblue-main-hardened`        | Silverblue | Reverse engineered Nouveau open source driver (not recommended)                              | Beta          |
| `silverblue-nvidia-hardened`      | Silverblue | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No            |
| `silverblue-nvidia-open-hardened` | Silverblue | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No            |


#### Kinoite (KDE Plasma)

| Name                           | Base    | NVIDIA Support                                                                               | ARM64 Support |
|--------------------------------|---------|----------------------------------------------------------------------------------------------|---------------|
| `kinoite-main-hardened`        | Kinoite | Reverse engineered Nouveau open source driver (not recommended)                              | Beta          |
| `kinoite-nvidia-hardened`      | Kinoite | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No            |
| `kinoite-nvidia-open-hardened` | Kinoite | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No            |

#### Sericea (Sway)

| Name                           | Base    | NVIDIA Support                                                                               | ARM64 Support |
|--------------------------------|---------|----------------------------------------------------------------------------------------------|---------------|
| `sericea-main-hardened`        | Sericea | Reverse engineered Nouveau open source driver (not recommended)                              | Beta          |
| `sericea-nvidia-hardened`      | Sericea | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No            |
| `sericea-nvidia-open-hardened` | Sericea | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No            |

### [Experimental](#experimental)
{: #experimental}

Note that there are no ISOs available for experimental images. If you want to try out an experimental image, you can use `ujust rebase-secureblue` on an existing secureblue installation.

#### COSMIC

| Name                          | Base   | NVIDIA Support                                                                               | ARM64 Support |
|-------------------------------|--------|----------------------------------------------------------------------------------------------|---------------|
| `cosmic-main-hardened`        | COSMIC | Reverse engineered Nouveau open source driver (not recommended)                              | Beta          |
| `cosmic-nvidia-hardened`      | COSMIC | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No            |
| `cosmic-nvidia-open-hardened` | COSMIC | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No            |

## [Server](#server)
{: #server}
### [CoreOS](#coreos)
{: #coreos}
{% include alert.html type='note' content='After you finish setting up your <a href="https://fedoraproject.org/coreos/">Fedora CoreOS</a> installation, you will need to disable <code>zincati.service</code> before rebasing to securecore.' %}

| Name                                  | Base   | NVIDIA Support                                                                               | ZFS Support | ARM64 Support |
|---------------------------------------|--------|----------------------------------------------------------------------------------------------|-------------|---------------|
| `securecore-main-hardened`            | CoreOS | Reverse engineered Nouveau open source driver (not recommended)                              | No          | Beta          |
| `securecore-nvidia-hardened`          | CoreOS | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No          | No            |
| `securecore-nvidia-open-hardened`     | CoreOS | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No          | No            |
| `securecore-zfs-main-hardened`        | CoreOS | Reverse engineered Nouveau open source driver (not recommended)                              | Yes         | No            |
| `securecore-zfs-nvidia-hardened`      | CoreOS | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | Yes         | No            |
| `securecore-zfs-nvidia-open-hardened` | CoreOS | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | Yes         | No            |

### [IoT](#iot)
{: #iot}

| Name                           | Base | NVIDIA Support                                                                               | ZFS Support | ARM64 Support |
|--------------------------------|------|----------------------------------------------------------------------------------------------|-------------|---------------|
| `iot-main-hardened`            | IoT  | Reverse engineered Nouveau open source driver (not recommended)                              | No          | Beta          |
| `iot-nvidia-hardened`          | IoT  | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | No          | No            |
| `iot-nvidia-open-hardened`     | IoT  | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | No          | No            |
| `iot-zfs-main-hardened`        | IoT  | Reverse engineered Nouveau open source driver (not recommended)                              | Yes         | No            |
| `iot-zfs-nvidia-hardened`      | IoT  | Proprietary NVIDIA driver with closed source kernel modules (recommended for pre-Turing)     | Yes         | No            |
| `iot-zfs-nvidia-open-hardened` | IoT  | Proprietary NVIDIA driver with open source kernel modules (recommended for Turing and later) | Yes         | No            |

