---
title: "Images | secureblue"
description: "List of available secureblue hardened operating system images"
permalink: /images
---

# Images

## Table of Contents
{: #table-of-contents}
- [Security recommendation](#security-recommendation)
- [Desktop](#desktop)
  - [Stable](#stable)
  - [Beta](#beta)
  - [Experimental](#experimental)
- [Server](#server)


## [Security recommendation](#security-recommendation)

Our Silverblue images utilize GNOME, which is the only desktop environment that secures privileged Wayland protocols like screencopy. This means that on non-GNOME systems, applications can access screen content of the entire desktop. This implicitly includes the content of other applications. It\'s primarily for this reason that Silverblue images are recommended. KDE has <a href="https://invent.kde.org/plasma/xdg-desktop-portal-kde/-/issues/7">plans to fix this</a>. GNOME also provides <a href="https://gitlab.gnome.org/GNOME/gnome-desktop/-/issues/213">thumbnailer sandboxing</a> in Gnome Files, which mitigates <a href="https://scarybeastsecurity.blogspot.com/2016/11/0day-exploit-compromising-linux-desktop.html">attacks via thumbnailers</a>. This is a relative recommendation between the desktop environments available on secureblue. GNOME has some extra security niceties like the ones listed above, however it does not solve any of the fundamental issues with desktop Linux security. For more details, consult the table below.

| DE/WM      | Secures privileged Wayland protocols? | Sandboxes thumbnailers? | Stability    | Recommendation                                                                                           |
|------------|---------------------------------------|-------------------------|--------------|----------------------------------------------------------------------------------------------------------|
| GNOME      | Yes                                   | Yes                     | Stable       | Recommended                                                                                              |
| KDE Plasma | No                                    | No                      | Stable       | Not recommended until Plasma secures privileged Wayland protocols                                        |
| Sway       | No                                    | No                      | Stable       | Not currently recommended                                                                                |
| River      | Yes                                   | No                      | Beta         | Recommended for those interested in trying out a WM                                                      |
| Hyprland   | No                                    | No                      | Beta         | Not currently recommended                                                                                |
| Wayfire    | No                                    | No                      | Beta         | Not currently recommended                                                                                |
| COSMIC     | No                                    | No                      | Experimental | Not currently recommended                                                                                |


## [Desktop](#desktop)

<b>nvidia-open</b> images are recommended for systems with NVIDIA GPUs Turing or newer. These include the new <a href="https://github.com/NVIDIA/open-gpu-kernel-modules">open kernel modules</a> from NVIDIA, not Nouveau.<br><b>nvidia</b> images are recommended for systems with NVIDIA GPUs Pascal or older. These include the closed kernel modules from NVIDIA.

### [Stable](#stable)

#### Silverblue (GNOME)

| Name                                      | Base      | NVIDIA Support          |
|-------------------------------------------|-----------|-------------------------|
| `silverblue-main-hardened`                | Silverblue| No                      |
| `silverblue-nvidia-hardened`              | Silverblue| Yes, closed drivers     |
| `silverblue-nvidia-open-hardened`         | Silverblue| Yes, open drivers       |


#### Kinoite (KDE Plasma)

| Name                                      | Base      | NVIDIA Support          |
|-------------------------------------------|-----------|-------------------------|
| `kinoite-main-hardened`                   | Kinoite   | No                      |
| `kinoite-nvidia-hardened`                 | Kinoite   | Yes, closed drivers     |
| `kinoite-nvidia-open-hardened`            | Kinoite   | Yes, open drivers       |

#### Sericea (Sway)

| Name                                      | Base      | NVIDIA Support          |
|-------------------------------------------|-----------|-------------------------|
| `sericea-main-hardened`                   | Sericea   | No                      |
| `sericea-nvidia-hardened`                 | Sericea   | Yes, closed drivers     |
| `sericea-nvidia-open-hardened`            | Sericea   | Yes, open drivers       |

### [Beta](#beta)

{% include alert.html type='note' content='Learn about Wayblue in <a href="https://github.com/wayblueorg/wayblue">Wayblue\'s repository</a>.' %}

#### Wayfire

| Name                                      | Base                  | NVIDIA Support          |
|-------------------------------------------|-----------------------|-------------------------|
| `wayblue-wayfire-main-hardened`           | Wayblue-Wayfire       | No                      |
| `wayblue-wayfire-nvidia-hardened`         | Wayblue-Wayfire       | Yes, closed drivers     |
| `wayblue-wayfire-nvidia-open-hardened`    | Wayblue-Wayfire       | Yes, open drivers       |

#### Hyprland

| Name                                      | Base                  | NVIDIA Support          |
|-------------------------------------------|-----------------------|-------------------------|
| `wayblue-hyprland-main-hardened`          | Wayblue-Hyprland      | No                      |
| `wayblue-hyprland-nvidia-hardened`        | Wayblue-Hyprland      | Yes, closed drivers     |
| `wayblue-hyprland-nvidia-open-hardened`   | Wayblue-Hyprland      | Yes, open drivers       |

#### River

| Name                                      | Base                  | NVIDIA Support          |
|-------------------------------------------|-----------------------|-------------------------|
| `wayblue-river-main-hardened`             | Wayblue-River         | No                      |
| `wayblue-river-nvidia-hardened`           | Wayblue-River         | Yes, closed drivers     |
| `wayblue-river-nvidia-open-hardened`      | Wayblue-River         | Yes, open drivers       |


#### Sway

| Name                                      | Base                  | NVIDIA Support          |
|-------------------------------------------|-----------------------|-------------------------|
| `wayblue-sway-main-hardened`              | Wayblue-Sway          | No                      |
| `wayblue-sway-nvidia-hardened`            | Wayblue-Sway          | Yes, closed drivers     |
| `wayblue-sway-nvidia-open-hardened`       | Wayblue-Sway          | Yes, open drivers       |

### [Experimental](#experimental)

#### COSMIC

| Name                                      | Base                  | NVIDIA Support          |
|-------------------------------------------|-----------------------|-------------------------|
| `cosmic-main-hardened`                    | COSMIC                | No                      |
| `cosmic-nvidia-hardened`                  | COSMIC                | Yes, closed drivers     |
| `cosmic-nvidia-open-hardened`             | COSMIC                | Yes, open drivers       |

## [Server](#server)

{% include alert.html type='note' content='After you finish setting up your <a href="https://fedoraproject.org/coreos/">Fedora CoreOS</a> installation, you will need to disable <code>zincati.service</code> before rebasing to securecore.' %}

| Name                                      | Base      | NVIDIA Support          | ZFS Support |
|-------------------------------------------|-----------|-------------------------|-------------|
| `securecore-main-hardened`                | CoreOS    | No                      | No          |
| `securecore-nvidia-hardened`              | CoreOS    | Yes, closed drivers     | No          |
| `securecore-nvidia-open-hardened`         | CoreOS    | Yes, open drivers       | No          |
| `securecore-zfs-main-hardened`            | CoreOS    | No                      | Yes         |
| `securecore-zfs-nvidia-hardened`          | CoreOS    | Yes, closed drivers     | Yes         |
| `securecore-zfs-nvidia-open-hardened`     | CoreOS    | Yes, open drivers       | Yes         |
