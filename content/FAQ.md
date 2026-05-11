---
title: "FAQ | secureblue"
description: "Answers to frequently asked questions about secureblue"
permalink: /faq
---

# FAQ
{: #faq}

## [Table of contents](#table-of-contents)
{: #table-of-contents}

- [Project information](#project)
  - [Why secureblue?](#why-secureblue)
  - [Is secureblue immutable?](#immutable)
  - [Why trust secureblue?](#why-trust-secureblue)
  - [What are the official secureblue communication channels?](#comms)
  - [What is the difference between Qubes OS and secureblue?](#qubes)
  - [Why not upstream your changes?](#upstream)
  - [Is this an install script?](#script)
  - [Another security project has a feature that's missing in secureblue, can you add it?](#feature-request)
  - [How do I get notified of new releases?](#releases)
  - [What do the GitHub releases involve?](#release-content)

- [System information](#system)
  - [Why is Flatpak included? Should I use Flatpak?](#flatpak)
  - [Should I use Electron apps? Why don't they work well with hardened_malloc?](#electron)
  - [Should I use Firejail?](#firejail)
  - [Why am I being asked to enroll a Secure Boot key?](#new-key)
  - [Why does secureblue include Homebrew?](#brew)
  - [Does secureblue use "linux-hardened"?](#linux-hardened)
  - [Why are upgrades so large?](#upgrade-size)

- [Usage](#usage)
  - [How do I update the system?](#update)
  - [How do I disable automatic updates?](#disable-update)
  - [Why am I receiving so many vulnerability patch notifications?](#update-notifications)
  - [How do I whitelist a module?](#module-whitelist)
  - [How do I install software?](#software)
  - [How do I install my VPN?](#vpn)
  - [How is gaming on secureblue?](#gaming)
  - [How do I install Steam?](#steam)
  - [How do I enable anti-cheat support?](#anticheat)
  - [How do I install Docker?](#docker)
  - [How do I run virtual machines?](#libvirt)
  - [How do I install additional fonts?](#fonts)
  - [How do I enable printing?](#printing)
  - [Why am I unable to start containers?](#container-userns)
  - [How do I allow a specific container to be run?](#container-policy)
  - [How do I enable userns for other apps?](#unconfined-userns)
  - [How do I manage potentially dangerous files or attachments?](#safe-pdfs)
  - [Why are Bluetooth kernel modules disabled? How do I enable them?](#bluetooth)
  - [How do I disable webcam kernel modules?](#webcam)
  - [How do I provision signed Distroboxes?](#distrobox-assemble)
  - [How do I customize secureblue?](#customization)
  - [How do I add a repo?](#adding-repos)
  - [How do I install proprietary codecs?](#install-codecs)
  - [How do I install KDE Connect?](#kde-connect)
  - [How do I change my DE?](#change-de)
  - [How do I enable kernel modules?](#enable-kernel-modules)
  - [How do I install an app as a PWA?](#pwa)
  - [How do I configure GRUB?](#configure-grub)


- [Troubleshooting](#troubleshooting)
  - [Something broke! How do I rollback?](#rollback)
  - [Why can't I install new KDE themes?](#ghns)
  - [Why doesn't my Xwayland app work?](#xwayland)
  - [Why do some commands fail when run with run0? (Exit code 203)](#run0-failures)
  - [Why I can't install nor use any GNOME user extensions?](#gnome-extensions)
  - [An app I use won't start due to a memory allocator issue. How do I fix it?](#standard-malloc)
  - [My clock is wrong, and it's not getting automatically set. How do I fix this?](#clock)
  - [My fans are really loud, is this normal?](#fans)
  - [On secureblue half of my CPU cores are gone. Why is this?](#smt)
  - [Why don't my AppImages work?](#appimage)
  - [Why don't my games/mods/mod managers work?](#anticheat-troubleshoot)
  - [Why won't Trivalent start when Bubblejailed?](#trivalent-bubblejail)
  - [Why won't Trivalent start on Nvidia?](#trivalent-nvidia)
  - [Why doesn't/won't/can't Trivalent...?](#trivalent-faq)
  - [Why is my splash screen disabled on KDE?](#kde-splash-disabled)
  - [Why is my secureblue virtual machine integration broken?](#vm-integration)
  - [Why can't I see any network services? (e.g. printers, Google Cast, file servers, IoT)](#mdns-resolution)
  - [Why is my DNS broken when using a VPN?](#dns-vpn)
  - [Why isn't my network adapter working?](#network-mac)
  - [Why doesn't VeraCrypt/iwd work?](#crypto-api)

<hr>

## [Project information](#project)
{: #project}

### [Why secureblue?](#why-secureblue)
{: #why-secureblue}

secureblue is a collaborative effort to ship a maximally secure Linux operating system. It leverages [bootable container](https://github.com/containers/bootc) technology to build on top of Fedora Atomic, avoiding the need to become a distro in the traditional sense. secureblue has benefitted massively by not being a distro, and instead shipping as bootable OCI container images. This has meant a ton of overhead is taken care of for us by Fedora. We don’t need general repos or packaging, except for a handful of specific packages ([Trivalent](https://github.com/secureblue/Trivalent), [hardened_malloc](https://github.com/GrapheneOS/hardened_malloc), etc). The Fedora Atomic ecosystem is also rich in tooling and automation (see: [BlueBuild](https://blue-build.org/)), plus the backdrop of robust container technology that already exists. All of this has largely enabled us to focus our energy on improving secureblue's hardening and UX, developing [Trivalent](https://github.com/secureblue/Trivalent), and building out userspace SELinux policies.

### [Is secureblue immutable?](#immutable)
{: #immutable}

"Immutable" is an old misnomer for atomic systems. It gives the impression that users can't modify or tinker with their system, which is not the case. While directories like `/usr` are mounted read-only by default, settings and configurations can be easily overriden with changes in `/etc`, which is not mounted read-only. This is in addition to the fact that `/usr` is mutated with every deployment that is staged and booted via any `rpm-ostree` operation (like upgrading, installing a new package, etc). As such, secureblue is not immutable.

### [Why trust secureblue?](#why-trust-secureblue)
{: #why-trust-secureblue}

secureblue uses several complementary mechanisms to protect against a variety of supply chain attack vectors, including vectors like rogue maintainers and theft of maintainer signing keys. For more information on these mechanisms, see the Build Architecture [article](/articles/build-architecture).

### [What are the official secureblue communication channels?](#comms)
{: #comms}

The secureblue [Bluesky account](https://bsky.app/profile/secureblue.dev), GitHub [release notes](https://github.com/secureblue/secureblue/releases), and [official email account](mailto:secureblueadmin@proton.me) are the only official secureblue communication channels. All other accounts and communications are not official secureblue communications.

### [What is the difference between Qubes OS and secureblue?](#qubes)
{: #qubes}

Qubes OS is a security-oriented desktop operating system based on Fedora Linux and the Xen hypervisor for those who want to use virtualization to isolate and compartmentalize. You can use and make templates to run various operating systems inside of "qubes". Isolation between different qubes is significantly stronger than the isolation between apps in traditional desktop operating systems, so Qubes OS can fill certain security needs better than secureblue. For example, untrusted files can only compromise the qube that the file is opened in.

secureblue is a Linux operating system with known security holes filled and proactive exploit mitigations in place. Virtualization [works on secureblue](#libvirt), but is not its focus. secureblue lacks [several virtualization features](https://www.qubes-os.org/doc/) that Qubes OS has. secureblue also places emphasis on usability, while Qubes OS is considered by many to have a steep learning curve.

There [is interest](https://github.com/QubesOS/qubes-issues/issues/9755) for a secureblue template in Qubes OS, but secureblue relies on Wayland which Qubes OS does [not yet support](https://github.com/QubesOS/qubes-issues/issues/3366).

### [Why not upstream your changes?](#upstream)
{: #upstream}

When possible, we do upstream our changes. In particular, we have contributed several findings and fixes to KDE. However, it's important to note that many of the changes we make are not possible to upstream, generally due to upstream not desiring them. This is for good reason. Many of the changes secureblue makes will necessarily break someone’s use case by default. Otherwise, secureblue could just submit all of our changes upstream to Fedora. Take AppImage support as an example. AppImages depend on the suid-root, deprecated, unmaintained fuse2 interface. They also encourage users to follow the security antipattern of downloading and executing binaries from the browser. Yet, since AppImages are widely used, Fedora can’t remove support for them. secureblue is willing to make these kinds of changes by default to improve security, with mechanisms available for users to re-enable support if needed for their use cases.

### [Is this an install script?](#script)
{: #script}

No. secureblue is not an install script, nor an add-on to a Fedora installation, nor a distro in the traditional sense. It is a set of [bootable container](https://github.com/containers/bootc) images shipped via GitHub's container registry. These images are rebuilt daily and pushed to GitHub's container registry. These images are then pulled in by `rpm-ostree`, which stages updates as a pending deployment for the next boot. To view information about your current local deployments and remotes, run `rpm-ostree status`.

### [Another security project has a feature that's missing in secureblue, can you add it?](#feature-request)
{: #feature-request}

First, check our [features list](/features) to see whether it already lists an equivalent or better feature. If it doesn't, open a new [GitHub issue](https://github.com/secureblue/secureblue/issues).

### [How do I get notified of new releases?](#releases)
{: #releases}

To subscribe to release notifications, on the secureblue GitHub page, click "Watch", and then "Custom", and select Releases like so:

<img alt="GitHub screenshot" src="/assets/release-notifications.png" />

If you prefer to use an Atom feed, supported by many RSS clients, you can use the [feed provided by GitHub](https://github.com/secureblue/secureblue/releases.atom).

### [What do the GitHub releases involve?](#release-content)
{: #release-content}

Substantial testing for new changes is done in the `staging` and `next` branches. However, once a commit is merged into `live`, a new set of builds is immediately generated and deployed. As such, the GitHub releases are an informational measure to track progress and communicate changes to users. This is only the case for the secureblue main repo, it isn't the case for Trivalent. For Trivalent, GitHub releases correspond to RPM releases to the RPM repo.

<hr>

## [System information](#system)
{: #system}

### [Why is Flatpak included? Should I use Flatpak?](#flatpak)
{: #flatpak}

Consult our [Flatpak article](/articles/flatpak).

### [Should I use Electron apps? Why don't they work well with hardened_malloc?](#electron)
{: #electron}

For Electron apps like Signal, Slack, FreeTube, Element, VSCode, Discord, etc., consult this [discussion](https://github.com/secureblue/secureblue/issues/193#issuecomment-1953323680).

### [Should I use Firejail?](#firejail)
{: #firejail}

[No](https://madaidans-insecurities.github.io/linux.html#firejail), use `bubblejail` if there's no Flatpak available for an app that you want to sandbox. Note that this requires [enabling unconfined user namespaces](#unconfined-userns), which is a security degradation.

### [Why am I being asked to enroll a Secure Boot key?](#new-key)
{: #new-key}

As part of a move to unify our supply chain, secureblue moved to our own kernel signing system. This was done for both practical and security advantages, and paved the way towards further kernel security improvements in the future. This change included a transition to a new secureblue Secure Boot key. You must enroll this new key to prevent issues when loading kernel modules:

```
ujust enroll-secureblue-secure-boot-key
```

### [Why does secureblue include Homebrew?](#brew)
{: #brew}

Homebrew is a cross-platform package manager, originally for macOS that allows users on Atomic systems to install CLI tools without layering and rebooting their system. It also brings with it a recent [independent security audit](https://github.com/trailofbits/publications/blob/master/reviews/2023-08-28-homebrew-securityreview.pdf) and subsequent [actions](https://github.com/Homebrew/brew.sh/blob/master/_posts/2024-07-30-homebrew-security-audit.md?plain=1#L24) taken in response to security findings uncovered by that audit.

### [Does secureblue use "linux-hardened"?](#linux-hardened)
{: #linux-hardened}

"linux-hardened" is the brand name for a specific set of kernel patches and builds on top of the mainline kernel, used by some distributions. secureblue doesn't use this kernel. Instead, we apply runtime configuration changes on top of Fedora's kernel. We can accomplish much but not all of what linux-hardened accomplishes using this approach. In the future, we plan to build our own kernel with patches on top of Fedora's kernel, including the [OpenPAX patches](https://github.com/edera-dev/linux-openpax). However, even today there are some important ways in which our approach is preferable. For example, linux-hardened completely disables [unprivileged user namespaces](/articles/userns). This means that to use flatpaks or Chromium-based browsers, [suid-root](https://en.wikipedia.org/wiki/Setuid) binaries are required. This is a significant security degradation. secureblue on the other hand implements SELinux-confined unprivileged user namespaces, restricting them by default but allowing them for Flatpaks and Trivalent to enable their operation without suid-root.

### [Why are upgrades so large?](#upgrade-size)
{: #upgrade-size}

This is an issue with rpm-ostree image-based systems generally, and not specific to secureblue. Ideally, upgrades would come in the form of a zstd-compressed container diff, but it's not there yet. Check out [this upstream issue](https://github.com/coreos/rpm-ostree/issues/4012) for more information.

<hr>

## [Usage](#usage)
{: #usage}

### [How do I update the system?](#update)
{: #update}

All system updates are automatic, running on at least a daily cadence. This includes automatic updates for rpm-ostree, Homebrew, Flatpak, and Podman. If the system is over 1 week out of date (for example in the event of update failures), you will be notified and told how to manually upgrade.

If you need to update your system manually, for example after a severe CVE is patched, run `ujust update-system`. To see whether an update is already staged for the next boot, run `rpm-ostree status`. More information can be found in the [rpm-ostree administrator documentation](https://coreos.github.io/rpm-ostree/administrator-handbook/#administering-an-rpm-ostree-based-system).

### [How do I disable automatic updates?](#disable-update)
{: #disable-update}

{% include alert.html type='caution' content='Disabling automatic updates is a security degradation. You will no longer automatically receive security updates.' %}

- `systemctl disable rpm-ostreed-automatic.timer` disables automatic system updates. To update manually, run `ujust update-system`.
- `systemctl disable flatpak-system-update.timer` and `systemctl disable --global flatpak-user-update.timer` disable automatic updates for system flatpaks and user flatpaks, respectively. To update manually, run `flatpak update`.
- `systemctl disable --global brew-upgrade.timer brew-update.timer` disables automatic Homebrew updates. To update manually, run `brew update && brew upgrade`.
- `systemctl disable podman-auto-update.timer` and `systemctl disable --global podman-auto-update.timer` disable automatic Podman container updates for system and user containers, respectively. To update manually, use `podman update` on your containers.

### [Why am I receiving so many vulnerability patch notifications?](#update-notifications)
{: #update-notifications}

Notifications are issued when the system detects kernel updates, Trivalent updates, and any other package updates that fix a [CVE](https://en.wikipedia.org/wiki/Common_Vulnerabilities_and_Exposures). All kernel updates trigger a notification because all kernel updates contain bugfixes, and [all kernel bugs are security bugs](http://www.kroah.com/log/blog/2026/01/02/linux-kernel-security-work/). All Trivalent updates trigger a notification because Trivalent updates are pushed when Chromium CVEs are patched upstream.

To check which packages triggered a vulnerability patch notification, check `rpm-ostree status --verbose` and `rpm-ostree db diff`. Note that the quantity and frequency of these notifications is not an indication of a higher rate of security issues. On the contrary, these are security issues which would face any desktop linux system. Unlike those systems however, secureblue ensures that the user is promptly informed when any security-relevant patch is available.

### [How do I whitelist a module?](#module-whitelist)
{: #module-whitelist}

secureblue prevents [numerous modules](https://github.com/secureblue/secureblue/blob/live/files/system/usr/lib/modprobe.d/secureblue.conf) from loading to reduce attack surface. If there's a particular module you need, run `ujust override-enable-module mod_name`. To undo this, run `ujust override-reset-module mod_name`.

### [How do I install software?](#software)
{: #software}

1. Check if it's already installed using `rpm -qa | grep x`
2. For GUI packages, you can install the Flatpak if available using the Software store or using `flatpak install`. You can browse this [catalogue of Flatpaks](https://flathub.org) to discover the available packages.
3. For CLI packages, you can install from brew if available using `brew install`. You can browse this [catalogue of Homebrew Formulae](https://formulae.brew.sh) to discover the available formulae.
4. If a package isn't available via the other two options, or if a package requires greater system integration, `rpm-ostree install` can be used to layer rpms directly into your subsequent deployments.

You can add the unfiltered Flathub repo with `ujust enable-flathub-unfiltered`.

### [How do I install my VPN?](#vpn)
{: #vpn}
To install a standalone VPN app, use `ujust install-vpn`.
Alternatively, you can download a WireGuard profile config from your VPN provider's website and import it via the system network settings menu. Below are the steps for GNOME.

1. Navigate to the "Network" tab in GNOME settings.
<img alt="Gnome Settings screenshot" src="/assets/gnome-settings-vpn-step1.png" />
2. Click the "+" button to add a new VPN connection.
<img alt="Gnome Settings screenshot" src="/assets/gnome-settings-vpn-step2.png" />
3. Click "Import from file..." and use the file chooser to select the VPN config file you downloaded from your VPN provider.
<img alt="Gnome Settings screenshot" src="/assets/gnome-settings-vpn-step3.png" />
4. Click the "Add" button, and your VPN will now be accessible through the system network settings.
<img alt="Gnome Settings screenshot" src="/assets/gnome-settings-vpn-step4.png" />

{% include alert.html type='note' content='If you get an error that says "Cannot Import VPN", that is likely because the name of the WireGuard configuration file is too long. GNOME Settings will only accept WireGuard configuration files with filenames 15 characters or less.' %}

### [How is gaming on secureblue?](#gaming)
{: #gaming}

Broadly speaking, gaming support on secureblue is similar to gaming on mainstream desktop Linux distros such as Fedora: if a game can be run on desktop Linux, you should be able to run it on secureblue.

However, some hardening is enabled by default that may need to be disabled for certain games to run. For example, many games require [Xwayland](#xwayland) to be enabled, some games require [anticheat support](#anticheat), and 32-bit programs require [enabling 32-bit support](/articles/kargs#32bit).

Additionally, some kernel arguments have a negative performance impact. The most impactful for multithreaded games is [disabling SMT](#smt). A few other kernel arguments have a negative performance impact but those are much more minor.

### [How do I install Steam?](#steam)
{: #steam}

```
ujust install-steam
```

### [How do I enable anti-cheat support?](#anticheat)
{: #anticheat}

{% include alert.html type='note' content='Kernel-level anti-cheat solutions are generally unsupported on desktop Linux.<br>You may want to search <a href="https://areweanticheatyet.com">Are We Anti-Cheat Yet</a> if a game doesn&#39;t work.' %}

Anti-cheat solutions typically require process tracing to work - the ability to monitor syscalls (and other signals) from other processes. On Linux, process tracing is controlled by the `kernel.yama.ptrace_scope` kernel parameter. [By default, secureblue doesn't allow ptrace attachment](https://github.com/secureblue/secureblue/blob/live/files/system/etc/sysctl.d/61-ptrace-scope.conf) at all, addressing [basic security concerns](https://www.kernel.org/doc/html/latest/admin-guide/LSM/Yama.html). The command below toggles between this restrictive default setting where `ptrace_scope` is set to `3`, breaking anti-cheat software, and a much less restrictive setting where `ptrace_scope` is set to `1`, which allows parent processes to trace child processes, enabling some anti-cheat solutions to work.

```
ujust toggle-anticheat-support
```

The ujust above is aliased as `toggle-ptrace-scope`. You must reboot your computer after running it.

### [How do I install Docker?](#docker)
{: #docker}

```
ujust install-docker
```

Similarly, you can uninstall Docker with:

```
ujust uninstall-docker
```

Consider using Podman over Docker as it is already installed on secureblue images.

### [How do I run virtual machines?](#libvirt)
{: #libvirt}

[Libvirt](https://libvirt.org/index.html), [QEMU](https://www.qemu.org/), the
[libvirt KVM/QEMU drivers](https://libvirt.org/drvqemu.html), and
[virt-manager](https://virt-manager.org/) (also called "Virtual Machine
Manager") are preinstalled on secureblue desktop images. You can run VMs in
virt-manager using either the QEMU user session or the QEMU system session.

The QEMU user session can be set up in virt-manager by clicking on "Add
connection" in the "File" menu and selecting "QEMU/KVM User Session" as the
hypervisor. The user session runs as an unprivileged user and works for most
simple use-cases; however, it does not support certain advanced features such
as some more complex networking setups.

The QEMU system session requires connecting to privileged
[libvirt daemons](https://libvirt.org/daemons.html), which must be enabled
first. To enable the libvirt modular daemons (which is recommended over using
the legacy monolithic `libvirtd` daemon), you can run the command
`ujust set-libvirt-daemons`. Connecting to the QEMU system session requires
authenticating as an administrator.

{% include alert.html type='caution' content='Some online guides suggest adding your user to the <code>libvirt</code> group to avoid having to authenticate when connecting to the QEMU system session. It is strongly recommended that you do <strong>not</strong> do this, as unrestricted access to the libvirt socket can be exploited to gain root access.' %}

### [How do I install additional fonts?](#fonts)
{: #fonts}

You can either layer fonts with `rpm-ostree install` (if they're packaged as an RPM) or add them to your user's local font directory at `$HOME/.local/share/fonts`.

If you install fonts in `$HOME/.local/share/fonts`, it is recommended to run the following command afterward to ensure the fonts have the correct SELinux labels:

```
restorecon -Rv $HOME/.local/share/fonts
```

### [How do I enable printing?](#printing)
{: #printing}

To enable printing using [CUPS](https://en.wikipedia.org/wiki/CUPS), run `ujust toggle-cups`. Note that this enables printing support, but still leaves printer discovery disabled for security reasons. The CUPS printer discovery service increases attack surface significantly and has a recent history of [severe vulnerabilities](https://www.redhat.com/en/blog/red-hat-response-openprinting-cups-vulnerabilities).

### [Why am I unable to start containers?](#container-userns)
{: #container-userns}

Software such as Podman and Distrobox need to be able to create user namespaces to work without root. The privilege to do so is denied by default in secureblue, but can be granted by running the following command:

```
ujust set-container-userns on
```

Trying to start a container without first enabling the ability toggled by the ujust above will result in an `OCI permission denied` error, but beware that enabling it results in a security degradation. Consult our [user namespaces article](/articles/userns) for more details.

### [How do I allow a specific container to be run?](#container-policy)
{: #container-policy}

Podman uses a [container policy](https://github.com/containers/image/blob/main/docs/containers-policy.json.5.md) to determine which container images are allowed to run. Secureblue sets this policy to reject container images by default, while allowing a short list of images as long as they pass a signature verification requirement.

When an image is rejected by policy, it produces an error message along the lines of:

> Source image rejected: Running image [...] is rejected by policy.

To manage container policy, you can use [`podman image trust`](https://docs.podman.io/en/latest/markdown/podman-image-trust.1.html). For example, to allow all images from `registry.fedoraproject.org/fedora` to run without signature verification, you can run

```sh
run0 podman image trust set -t accept registry.fedoraproject.org/fedora
```

The same command without `run0` will set this policy for the current user only.

To reset container policy to the system default, run:

```sh
rm -f ~/.config/containers/policy.json
run0 cp /usr/etc/containers/policy.json /etc/containers/policy.json
```

### [How do I enable userns for other apps?](#unconfined-userns)
{: #unconfined-userns}

The following command will toggle the ability of processes in the unconfined SELinux domain to create user namespaces. It's necessary for any apps that require this feature, such as: browsers other than Trivalent, many [Electron](https://en.wikipedia.org/wiki/Electron_(software_framework)) apps, and bubblejail.

```
ujust set-unconfined-userns on
```

Attempting to bubblewrap a program without first enabling the ability toggled by the ujust above will result in a `bwrap: Creating new namespace failed: Permission denied` error, but beware that enabling it results in a security degradation. Consult our [user namespaces article](/articles/userns) for more details.

### [How do I manage potentially dangerous files or attachments?](#safe-pdfs)
{: #safe-pdfs}

The program [Dangerzone](https://dangerzone.rocks/) is designed to sanitize potentially dangerous PDFs, office documents, or images in a sandboxed environment. To install Dangerzone, run:

```
ujust install-dangerzone
```

Note that this comes with a security trade-off: it requires enabling [container-domain user namespaces](#container-userns) and "admin-only attach" ptrace (`ptrace_scope` is set to `2`), allowing privileged users to attach to or trace child processes. Dangerzone runs Podman under the hood, and requires [gVisor](https://gvisor.dev/) to run document processing workloads in an isolated sandbox, [which needs Linux's ptrace subsystem to intercept system calls](https://gvisor.dev/blog/2024/09/23/safe-ride-into-the-dangerzone/).

### [Why are Bluetooth kernel modules disabled? How do I enable them?](#bluetooth)
{: #bluetooth}

Bluetooth has a long and consistent history of security issues. However, if you still need it, run:

```
ujust set-bluetooth-modules on
```

### [How do I disable webcam kernel modules?](#webcam)
{: #webcam}

You can disable webcam kernel modules by running the following:

```
ujust set-webcam-modules off
```

Note that this requires a reboot.

### [How do I provision signed Distroboxes?](#distrobox-assemble)
{: #distrobox-assemble}

```
ujust distrobox-assemble
```

Note that Distrobox is not a security tool. It focuses on [integration, not isolation](https://distrobox.it/#security-implications), and [there are no plans to change this](https://github.com/89luca89/distrobox/issues/28).

### [How do I customize secureblue?](#customization)
{: #customization}

If you want to add your own customizations on top of secureblue that go beyond installing packages, unless you are [contributing](/contributing), you are advised strongly against forking. Instead, create a repo for your own image by using the [BlueBuild template](https://github.com/blue-build/template), then change your `base-image` to a secureblue image. This allows you to apply your customizations to secureblue in a concise and maintainable way, without the need to constantly sync with upstream. For secureblue development purposes, forking then [building with GitHub Actions](/contributing#building-ga) is the recommended approach.

### [How do I add a repo?](#adding-repos)
{: #adding-repos}

The process of adding a repository to secureblue is the same as [on Fedora](https://docs.fedoraproject.org/en-US/quick-docs/adding-or-removing-software-repositories-in-fedora/#_for_fedora_41_or_later_dnf_5).

### [How do I install proprietary codecs?](#install-codecs)
{: #install-codecs}

There is no need, they are already included in the image.

### [How do I install KDE Connect?](#kde-connect)
{: #kde-connect}

You can install `kde-connect` by running `rpm-ostree install kde-connect` and then rebooting. After you do that, you have to permanently enable the 'kdeconnect' service in the firewall configuration in order for devices to recognize and be recognizable via KDE Connect.

### [How do I change my DE?](#change-de)
{: #change-de}

Choose whatever you like from the [available options](https://secureblue.dev/images) by running `ujust rebase-secureblue`.

### [How do I enable kernel modules?](#enable-kernel-modules)
{: #enable-kernel-modules}

Some functionality requires you to enable extra kernel modules that are disabled by default in secureblue. Modules can be enabled by running `ujust override-enable-module`. For instance, mounting SMB shares requires the `cifs` and `netfs` kernel modules. To load them, simply run `ujust override-enable-module cifs` and `ujust override-enable-module netfs` then reboot.

### [How do I install an app as a PWA?](#pwa)
{: #pwa}

It is recommended to install apps whose clients would otherwise use Electron, such as Discord, Spotify, Element, YouTube, etc. as a PWA on Trivalent. Consult the [Google Chrome support page](https://support.google.com/chrome/answer/9658361) for instructions on installing a PWA.

### [How do I configure GRUB?](#configure-grub)
{: #configure-grub}

As of Fedora 41, GRUB configuration is now [static](https://discussion.fedoraproject.org/t/etc-default-grub-is-missing-on-silverblue-41-fresh-install/135344/3) for Atomic desktops, not dynamic <sup>([why?](https://discussion.fedoraproject.org/t/etc-default-grub-is-missing-on-silverblue-41-fresh-install/135344/23))</sup>. This means that changes to the main config file at `/boot/grub2/grub.cfg` are expected to come from upstream, not from manual configuration or tools like `grub2-mkconfig`. However, `/boot/grub2/grub.cfg` contains two if statements that check if files `/boot/grub2/user.cfg` and/or `/boot/grub2/console.cfg` exist and will then load in any manual configuration made in these files. `user.cfg` is intended for general configuration, whereas `console.cfg` is intended for graphics related configuration, however you can use either file for either purpose or use one for both if you wish. You can find information about manual grub configuration [here](https://wiki.archlinux.org/title/GRUB#Custom_grub.cfg).

Please note, the instructions provided by the Arch Wiki article for manually adding a menu entry for Windows are incorrect. The Wiki states you need to provide a `hints_string` as a parameter for the `search` function, however this is not required and will cause GRUB to error. You only need to provide the UUID for the partition that holds the Windows boot EFI file.

<hr>

## [Troubleshooting](#troubleshooting)
{: #troubleshooting}

### [Something broke! How do I rollback?](#rollback)
{: #rollback}

Each `rpm-ostree` operation generates and stages a new deployment, which includes the creation of a new GRUB entry at position 0. To boot into the previous deployment, simply select the GRUB entry at position 1. As a preventative measure, you can ensure you always have a known-good deployment available by [pinning](https://docs.fedoraproject.org/en-US/fedora-silverblue/faq/#_how_can_i_upgrade_my_system_to_the_next_major_version_for_instance_rawhide_or_an_upcoming_fedora_release_branch_while_keeping_my_current_deployment) an existing deployment.

### [Why can't I install new KDE themes?](#ghns)
{: #ghns}

The functionality that provides this, called GHNS, is disabled by default due to the risk posed by the installation of potentially damaging or malicious scripts. This has caused [real damage](https://blog.davidedmundson.co.uk/blog/kde-store-content/).

If you still want to enable this functionality, run:

```
ujust toggle-ghns
```

### [Why doesn't my Xwayland app work?](#xwayland)
{: #xwayland}

[Xwayland](https://wayland.freedesktop.org/docs/html/ch05.html#sect-X11-Application-Support-architecture) is disabled by default on GNOME, KDE Plasma, and Sway. Some apps haven't migrated to [Wayland](https://en.wikipedia.org/wiki/Wayland_(protocol)) yet, and will often say so when started from the terminal by complaining about not being able to find [X11](https://en.wikipedia.org/wiki/X.Org_Server). Since enabling Xwayland is a security degradation, you are encouraged to find alternatives for those apps. If you decide you still need Xwayland, run:

```
ujust set-xwayland on
```

### [Why do some commands fail when run with run0? (Exit code 203)](#run0-failures)
{: #run0-failures}

Fedora's SELinux policy causes `run0` to silently fail when run alongside some commands (e.g., dnf). There is already an [open issue](https://github.com/fedora-selinux/selinux-policy/issues/2500) tracking this, and it is expected to be fixed in the future. In the meantime, you can run `run0 -i <your command>` or enter a root shell by running `run0` on its own and run the command from there.

### [Why I can't install nor use any GNOME user extensions?](#gnome-extensions)
{: #gnome-extensions}

This is because support for installing & using them has been intentionally disabled by default in secureblue.
Only GNOME system extensions are trusted, if they are installed.

To enable support for installing GNOME user extensions, you can run ujust command:

```
ujust toggle-gnome-extensions
```

### [An app I use won't start due to a memory allocator issue. How do I fix it?](#standard-malloc)
{: #standard-malloc}

{% include alert.html type='note' content='This is a common issue for Electron apps. Program crashes triggered by hardened_malloc typically result in terminal output with the phrase "fatal allocator error".' %}

To disable hardened_malloc for a Flatpak app, remove the `LD_PRELOAD` environment variable via Flatseal. This change will persist until you re-enable it by replacing the removed environment variable or running `ujust harden-flatpak APP-ID` (replacing `APP-ID` with the ID of the app).

To run a non-Flatpak program (for example, installed via a layered package or Homebrew) with the standard memory allocator, you can wrap the command in `ujust with-standard-malloc`. This is not a persistent change; it only affects a single run of the program.

To make a non-Flatpak application _always_ launch with the standard memory allocator, you can edit its `.desktop` file:

1. Find the application's `.desktop` file and copy it to `~/.local/share/applications` in your home directory. (The `.desktop` files for layered packages are typically found in `/usr/share/applications`.)
2. Edit the `Exec=` line in your copy of the `.desktop` file to wrap the command in `ujust with-standard-malloc`. For example, `Exec=foo bar` would become `Exec=ujust with-standard-malloc foo bar`.

### [My clock is wrong, and it's not getting automatically set. How do I fix this?](#clock)
{: #clock}

If your system time is off by an excessive amount due to rare conditions like a CMOS reset, your network will not connect. A one-time manual reset will fix this. This should never be required except under very rare circumstances.

For more technical detail, see [issue #268](https://github.com/secureblue/secureblue/issues/268)

### [My fans are really loud, is this normal?](#fans)
{: #fans}

During rpm-ostree operations, it's normal. Outside of that, make sure you followed the [post-install instructions](/post-install).

### [On secureblue half of my CPU cores are gone. Why is this?](#smt)
{: #smt}

The [kernel argument](/articles/kargs) `mitigations=auto,nosmt` is set on secureblue. This means that if your CPU is vulnerable to attacks that utilize [Simultaneous Multithreading](https://en.wikipedia.org/wiki/Simultaneous_multithreading), SMT will be disabled. There are several other kernel arguments secureblue sets that may also trigger this behavior, including `nosmt=force`, and `l1tf=full,force`.

If SMT is disabled, this effectively halves the number of CPU cores; the performance impact of this can be significant (up to around 40%) for highly parallel, CPU-intensive workloads. On the other hand, for many workloads the impact is much smaller, and it can even slightly improve performance of single-threaded workloads.

### [Why don't my AppImages work?](#appimage)
{: #appimage}

AppImages depend on fuse2, which is unmaintained and depends on a SUID root binary. For this reason, fuse2 support is removed by default. It's strongly recommended that you find alternative mechanisms to install your applications (Flatpak, Distrobox, etc.). If you can't find an alternative and still need fuse2, you can add it back by layering with `rpm-ostree install fuse` and then rebooting.

### [Why don't my games/mods/mod managers work?](#anticheat-troubleshoot)
{: #anticheat-troubleshoot}

Some modding systems and anti-cheat solutions require process tracing to work, which is disabled by default on secureblue. For more information, see: [How do I enable anti-cheat support?](#anticheat)

### [Why won't Trivalent start when Bubblejailed?](#trivalent-bubblejail)
{: #trivalent-bubblejail}

`bubblejail` shouldn't be used on Trivalent, there are issues reported with the pairing and removing the `bubblejail` config after it is applied can be difficult. It should also be noted that applying additional sandboxing may interfere with Chromium's own internal sandbox, so it may end up reducing security.

### [Why won't Trivalent start on Nvidia?](#trivalent-nvidia)
{: #trivalent-nvidia}

On some Nvidia machines, Trivalent defaults to the X11 backend. Since secureblue disables Xwayland by default, this means that you will need to run `ujust set-xwayland on` and reboot for Trivalent to work.

### [Why doesn't/won't/can't Trivalent...?](#trivalent-faq)
{: #trivalent-faq}

For any other issues you experience with Trivalent, visit Trivalent's dedicated FAQ page by opening `chrome://trivalent-faq` in Trivalent.

### [Why is my splash screen disabled on KDE?](#kde-splash-disabled)
{: #kde-splash-disabled}

The KDE splash screen is currently [broken](https://github.com/secureblue/secureblue/issues/926) if Xwayland is disabled (which is the default on secureblue), due to an [upstream bug](https://discuss.kde.org/t/how-to-disable-xwayland-for-the-plasma-wayland-session/19325/6). secureblue automatically disables it for every user to work around this. If you don't want the splash screen to be automatically disabled, run the following command:

```
run0 rm /etc/xdg/ksplashrc
```

### [Why is my virtual machine integration broken?](#vm-integration)
{: #vm-integration}

In order to use SPICE integration with a secureblue guest, such as the shared clipboard and automatic display resolution setting, you must enable Xwayland:

```
ujust set-xwayland on
```

### [Why can't I see any network services? (e.g. printers, Google Cast, file servers, IoT)](#mdns-resolution)
{: #mdns-resolution}

For applications to automatically discover other devices on your network such as `fritz.box`, `sonos.local`, `fuchsia.local` or `epson.local`, you need to enable the Avahi mDNS stack and allow it through your firewall.

{% include alert.html type='caution' content='mDNS is insecure by design and allows any device on your local network to announce any name or service. Its implementations also have a history of security vulnerabilities. Avoid it where possible, such as by assigning static IPs to local devices, and only expose the mDNS port on trusted networks.' %}

#### Enabling Avahi

`avahi-daemon.service` and `avahi-daemon.socket` are masked and disabled by default. To undo this, run these commands in a root terminal, which you can access by running `run0`:

```
# systemctl unmask avahi-daemon.socket
# systemctl unmask avahi-daemon.service
# systemctl enable --now avahi-daemon.socket
# systemctl enable --now avahi-daemon.service
```

#### Allowing mDNS through your firewall

For Avahi to work, you must modify your firewall zone to allow mDNS traffic. But, if you modify the default zone (typically FedoraWorkstation), then mDNS will be open for all current and future connections unless specified. Instead, create a custom zone:

```
# firewall-cmd --new-zone=allow-mdns --permanent
# firewall-cmd --zone=allow-mdns --add-service=mdns --permanent
# firewall-cmd --reload
```

Then, modify only your current connection to use the new zone. Find your current connection:

```
# nmcli connection show
```

Then, change the zone for the connection (using the UUID from above) to `allow-mdns`.

```
# nmcli connection modify uuid ${UUID} connection.zone allow-mdns
```

Then apply your connection changes to the active device as shown by `nmcli`, such as `wlp1s0`:

```
# nmcli device reapply ${DEVICE}
```
### [Why is my DNS broken when using a VPN?](#dns-vpn)
{: #dns-vpn}

Some third-party VPN clients depend on systemd&#8209;resolved directly, rather than sending their connection information to NetworkManager. If your VPN is broken, consider importing the configuration, instead of using the Mullvad GUI or `wg-quick`. Several desktops, including GNOME, let you do this in Network Settings. Or, you can use the command line:

```
run0 nmcli connection import type wireguard file /path/to/vpn.conf
```

Optionally, you can then find the connection's name and set it to autoconnect:

```
nmcli connection show
run0 nmcli connection modify "Proton US123" connection.autoconnect yes
```

If this is not an option, such as for Tailscale (which directly conflicts with secureblue's default DNS), you can **switch back to systemd&#8209;resolved** which is the default in Fedora.

```
ujust dns-selector resolver resolved
```

### [Why isn't my network adapter working?](#network-mac)
{: #network-mac}

Some network adapters, especially USB ethernet adapters, can appear stuck in a "connecting" state or not function at all. The solution may be to disable MAC randomization:

```
ujust toggle-mac-randomization
```

### [Why doesn't VeraCrypt/iwd work?](#crypto-api)
{: #crypto-api}

The [userspace interface to the kernel crypto API](https://www.kernel.org/doc/html/latest/crypto/userspace-if.html) provides substantial attack surface and has been involved in various exploits, including [CVE-2026-31431 ("Copy Fail")](https://access.redhat.com/security/cve/cve-2026-31431). As a proactive security measure, secureblue uses SELinux policy to block most userspace processes from using this API by denying access to `AF_ALG` sockets.

However, some software does need access to this API. BlueZ (the Linux kernel's Bluetooth stack) is one notable example, and secureblue's SELinux policy grants the Bluetooth daemon access by default.

VeraCrypt and iwd also rely on the kernel crypto API and are not usable without it. (This is not an issue for most users: wpa_supplicant, not iwd, is the default wireless daemon on secureblue.) If you want to use these anyway, you can disable this custom SELinux policy with the following command:

```
run0 -i semodule -v -d secureblue_deny_alg_sockets
```

{% include alert.html type='caution' content='Disabling this SELinux policy is a security degradation.' %}
