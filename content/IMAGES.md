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
  - [Experimental](#experimental)
- [Server](#server)

## [Security recommendation](#security-recommendation)

GNOME and Sway (Silverblue and Sericea images, respectively) secure privileged Wayland protocols like screencopy. This means that on environments outside of GNOME and Sway, applications can access screen content of the entire desktop. This implicitly includes the content of other applications. It\'s primarily for this reason that Silverblue and Sericea images are recommended. A commit submitted by secureblue to fix this in KDE is scheduled to land in Plasma 6.4 (<a href="https://invent.kde.org/plasma/xdg-desktop-portal-kde/-/issues/7#note_1112249">discussion</a>, <a href="https://invent.kde.org/plasma/xdg-desktop-portal-kde/-/merge_requests/347">PR</a>). Cosmic has <a href="https://github.com/pop-os/cosmic-comp/issues/970">plans</a> to fix this.

In addition, GNOME also provides weak <a href="https://gitlab.gnome.org/GNOME/gnome-desktop/-/issues/213">thumbnailer sandboxing</a> in Gnome Files, which is an effort to mitigate <a href="https://scarybeastsecurity.blogspot.com/2016/11/0day-exploit-compromising-linux-desktop.html">attacks via thumbnailers</a>. No environment aside from GNOME provides any thumbnailer sandboxing.

It should also be noted that our Sericea images disable the wlroots desktop portal, despite it being commonly used alongside Sway. This is because the portal reintroduces the screencopy vulnerability described above, which would undermine the security improvements in Sway for sandboxed applications. The downside of this is that by default on our Sericea images, flatpaks and applications that haven't implemented protocol support (like chromium-based browsers) are entirely prevented from screenshotting and screensharing. If necessary, Sway users can configure this using their own <a href="https://flatpak.github.io/xdg-desktop-portal/docs/portals.conf.html">portals.conf</a>.

This section is a relative recommendation between the desktop environments available on secureblue. GNOME and Sway have some extra security niceties like the ones listed above. However, this should not be misconstrued as saying that either one solves any of the fundamental issues with desktop Linux security. For more details, consult the table below.

| DE/WM      | Secures privileged Wayland protocols? | Thumbnailer sandboxing? | Stability    | Recommendation                                                                                           |
|------------|---------------------------------------|-------------------------|--------------|----------------------------------------------------------------------------------------------------------|
| GNOME      | Yes                                   | Weak                     | Stable       | Recommended                                                                                              |
| KDE Plasma | No                                    | None                    | Stable       | Not recommended until Plasma secures privileged Wayland protocols (ETA: Plasma 6.4)                                       |
| Sway       | Yes                                    | None                    | Stable       | Recommended for tiling WM users                                                                                |
| COSMIC     | No                                    | None                    | Experimental | Not currently recommended                                                                                |


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
