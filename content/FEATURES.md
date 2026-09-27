---
title: "Features | secureblue"
description: "List of secureblue features"
permalink: /features
---

# Features

## [Exploit mitigation](#exploit-mitigation)
{: #exploit-mitigation}

- Install and enable [hardened_malloc](https://github.com/GrapheneOS/hardened_malloc) globally, including for Flatpaks.
- Install [Trivalent](https://github.com/secureblue/Trivalent), our security-focused, Chromium-based browser inspired by [Vanadium](https://github.com/GrapheneOS/Vanadium). <sup>[Why Chromium-based?](https://grapheneos.org/usage#web-browsing)</sup> <sup>[Why not a Flatpak?](https://forum.vivaldi.net/post/669805)</sup>
- Kernel hardening via sysctl. <sup>[details](https://github.com/secureblue/secureblue/blob/live/files/system/usr/lib/sysctl.d/55-hardening.conf)</sup>
- Kernel hardening via kernel arguments. <sup>[details](/articles/kargs)</sup>
- Configure chronyd to use Network Time Security (NTS).
- Configurable DNS over TLS and local DNSSEC validation with Unbound via `ujust dns-selector`. systemd&#8209;resolved is optionally available for compatibility.
- Install USBGuard and provide `ujust` commands to automatically configure it.

## [Trivalent: hardened default browser](#trivalent-browser)
{: #trivalent-browser}

secureblue includes Trivalent as the default browser. Trivalent is a hardened variant of Chromium providing enhanced security, inspired by GrapheneOS’s Vanadium.

Some of the features added compared to Google Chrome:

- SELinux [confinement](https://github.com/secureblue/Trivalent/blob/live/selinux/trivalent.te).
- Strict site isolation and sandboxed iframes
- JavaScript JIT disabled by default with per-site toggle via drop-down permission menu
- Add support to optionally enable the DrumBrake WebAssembly interpreter previously exclusive to Microsoft Edge, to support WebAssembly when JIT compilation is disabled
- Dynamic code execution is blocked for processes without JavaScript JIT enabled as an extension to the seccomp-bpf sandbox
- WebGPU disabled for attack surface reduction
- WebRTC IP handling policy toggle to control peer-to-peer WebRTC mode
- High performance content filtering engine with a per-site toggle using EasyList, EasyPrivacy and EasyList's Adblock Warning Removal List as the primary filters with EasyList's supplementary regional lists enabled based on enabled browser languages
- More complete state partitioning without origin trial opt-out
- High entropy client hints are replaced with the standard placeholder values used in Chromium's reduced user agent for both the browser and WebView to close a loophole where Chromium is still sharing the major OS version, device model and browser minor/build/patch version with any server requesting it via client hints
- Trivial subdomain hiding disabled

These features, as well as more technical features, can be found in [Trivalent’s patches directory](https://github.com/secureblue/Trivalent/tree/live/patches).

Better default settings, including non-user-facing flags:

- Reduce Accept-Language header by default (only available via chrome://flags)
- Third party cookies disabled by default
- Payment support disabled by default
- Website background sync disabled by default
- Protected media (DRM) disabled by default
- Hyperlink auditing disabled by default
- Do Not Track enabled by default mainly to avoid users differentiating themselves from others by enabling it since it has no real value
- WebRTC IP handling policy set to the most private value by default instead of the least private value

## [Filling known security holes](#filling-security-holes)
{: #filling-security-holes}

- Remove [suid-root](https://en.wikipedia.org/wiki/Setuid) from [numerous binaries](https://github.com/secureblue/secureblue/blob/live/files/scripts/removesuid.sh), replacing functionality [using capabilities](https://github.com/secureblue/secureblue/blob/live/files/scripts/removesuid.sh#L89), and remove `sudo`, `su`, and `pkexec` entirely in favor of `run0`. <sup>[why?](https://mastodon.social/@pid_eins/112353324518585654)</sup>
- Disable Xwayland by default (for GNOME, Plasma, and Sway images).
- Disable thumbnailing by default (for GNOME, Plasma, and Sway images).
- Disable install & usage of GNOME user extensions by default.
- Disable KDE GHNS by default. <sup>[why?](https://blog.davidedmundson.co.uk/blog/kde-store-content/)</sup>
- Remove the unmaintained and suid-root fuse2 by default.
- Disable unprivileged user namespaces by default for the unconfined SELinux domain and the container SELinux domain, while retaining support for Flatpaks, Trivalent, and other applications that need unprivileged user namespaces. <sup>[why?](/articles/userns)</sup>
- Prohibit ptrace attachment by default. <sup>[why?](https://www.kernel.org/doc/html/latest/admin-guide/LSM/Yama.html)</sup>
- Locking down Flatpak permissions to close sandbox escapes. <sup>[why?](/articles/flatpak)</sup>

## [Security by default](#security-by-default)
{: #security-by-default}

- Disable all ports and services for firewalld.
- Use HTTPS for all rpm mirrors.
- Set all default container policies to `reject`, `signedBy`, or `sigstoreSigned`.
- Enable only the [Flathub-verified](https://flathub.org/apps/collection/verified/1) remote by default.

## [Reducing information leakage](#info-leak)
{: #info-leak}

- Add per-network or per-connection MAC randomization.
- Disable coredumps.

## [Attack surface reduction](#attack-surface)
{: #attack-surface}

- Blacklist numerous unused kernel modules to reduce attack surface. <sup>[details](https://github.com/secureblue/secureblue/blob/live/files/system/usr/lib/modprobe.d/secureblue.conf)</sup>
- Use SELinux to restrict userspace access to various socket classes, including the kernel crypto API and IPSec sockets.
- Protect against brute force by locking user accounts for 24 hours after 50 failed login attempts, providing password quality suggestions and making use of hardened password hashing.
- Disable and mask a variety of services by default (including cups, geoclue, passim, and others).

## [Security ease-of-use](#security-ease)
{: #security-ease}

- Provide system auditing tooling to verify the status of system hardening and provide users with suggestions.
- Setup commands via `ujust` for installing desktop apps from common VPN providers.
- Install Bubblejail for additional sandboxing tooling.
- Provide tooling for automatically setting up and enabling LUKS TPM2+PIN integration for unlocking LUKS drives (on devices where the TPM is free of known vulnerabilities).
- Provide tooling for automatically setting up and enabling LUKS FIDO2 integration for unlocking LUKS drives.
- Provide toggles for a variety of the hardening set by default, for user convenience (`ujust --choose`).

## [General ease-of-use](#general-ease)
{: #general-ease}

- Provide out-of-the-box support for patent-encumbered codecs and drivers, which Fedora doesn't provide for legal reasons.
- Provide server images with the ZFS kmod and tooling preinstalled (`-zfs`).
- Provide images for any desired Nvidia driver and kmod configuration: `main` images with Nouveau + NVK, `nvidia` images with the Nvidia-closed kmod and drivers, and `nvidia-open` images with the Nvidia-open kmod and drivers.
- Provide tooling to easily change desktop environments by rebasing between our images (`ujust rebase-secureblue`).
- Install [Homebrew](https://brew.sh/), which provides an easy way to install and update many CLI tools, and set up Homebrew to use [brew-proxy](https://codeberg.org/HastD/brew-proxy) for additional security. <sup>[details](/faq#brew)</sup>
