---
title: "FAQ | secureblue"
description: "Answers to frequently asked questions about secureblue"
permalink: /faq
---

# FAQ
{: #faq}

## [Table of contents](#table-of-contents)
{: #table-of-contents}
- [Why secureblue?](#secureblue)
- [Why not upstream your changes?](#upstream)
- [Is this an install script?](#script)
- [Why is Flatpak included? Should I use Flatpak?](#flatpak)
- [Should I use Electron apps? Why don't they work well with hardened_malloc?](#electron)
- [My fans are really loud, is this normal?](#fans)
- [Should I use Firejail?](#firejail)
- [An app I use won't start due to a malloc issue. How do I fix it?](#standard-malloc)
- [On secureblue half of my CPU cores are gone. Why is this?](#smt)
- [How do I install software?](#software)
- [How do I install Steam?](#steam)
- [How do I enable anti-cheat support?](#anticheat)
- [How do I install Docker?](#docker)
- [Why am I unable to start containers?](#container-userns)
- [How do I enable userns for other apps?](#unconfined-userns)
- [Another security project has a feature that's missing in secureblue, can you add it?](#feature-request)
- [Why are Bluetooth kernel modules disabled? How do I enable them?](#bluetooth)
- [Why are upgrades so large?](#upgrade-size)
- [Why can't I install new KDE themes?](#ghns)
- [Why doesn't my Xwayland app work?](#xwayland)
- [Why I can't install nor use any GNOME user extensions?](#gnome-extensions)
- [My clock is wrong, and it's not getting automatically set. How do I fix this?](#clock)
- [How do I get notified of new releases?](#releases)
- [Why don't my AppImages work?](#appimage)
- [Why don't KDE Vaults work?](#kde-vaults)
- [How do I provision signed Distroboxes?](#distrobox-assemble)
- [Why won't Trivalent start when Bubblejailed?](#trivalent-bubblejail)
- [Why won't Trivalent start on Nvidia?](#trivalent-nvidia)
- [Why don't some websites that require JIT/WebAssembly work in Trivalent even with the V8 Optimizer toggle enabled?](#trivalent-v8-exceptions)
- [Why don't extensions work in Trivalent?](#trivalent-extensions)
- [How do I customize secureblue?](#customization)
- [How do I add a repo?](#adding-repos)
- [How do I install proprietary codecs?](#install-codecs)
- [How do I change my DE?](#change-de)
- [Why doesn't DRM content (spotify, netflix etc.) work in Trivalent?](#trivalent-protected-content)
- [How do I enable kernel modules?](#enable-kernel-modules)


### [Why secureblue?](#secureblue)
{: #secureblue}

secureblue is a collaborative effort to ship a maximally secure Linux operating system. It leverages [bootable container](https://github.com/containers/bootc) technology to build on top of Fedora Atomic, avoiding the need to become a distro in the traditional sense. secureblue has benefitted massively by not being a distro, and instead shipping as bootable OCI container images. This has meant a ton of overhead is taken care of for us by Fedora. We don’t need general repos or packaging, except for a handful of specific packages ([Trivalent](https://github.com/secureblue/Trivalent), [hardened_malloc](https://github.com/GrapheneOS/hardened_malloc), etc). The Fedora Atomic ecosystem is also rich in tooling and automation (see: [BlueBuild](https://blue-build.org/)), plus the backdrop of robust container technology that already exists. All of this has largely enabled us to focus our energy on improving secureblue's hardening and UX, developing [Trivalent](https://github.com/secureblue/Trivalent), and building out userspace SELinux policies.

### [Why not upstream your changes?](#upstream)
{: #upstream}

When possible, we do upstream our changes. For example, collaborating with KDE to make [portal improvements](https://invent.kde.org/plasma/xdg-desktop-portal-kde/-/merge_requests/347). However, it's important to note that many of the changes we make are not possible to upstream, generally due to upstream not desiring them. This is for good reason. Many of the changes secureblue makes will necessarily break someone’s use case by default. Otherwise, secureblue could just submit all of our changes upstream to Fedora. Take AppImage support as an example. AppImages depend on the suid-root, deprecated, unmaintained fuse2 interface. They also encourage users to follow the security antipattern of downloading and executing binaries from the browser. Yet, since AppImages are widely used, Fedora can’t remove support for them. secureblue is willing make these kinds of changes by default to improve security, with mechanisms available for users to re-enable support if needed for their use cases.

### [Is this an install script?](#script)
{: #script}

No. When you run our installer, you are *fully replacing* the system. secureblue is not an install script, nor an addon to a Fedora installation, nor a distro in the traditional sense. It is a set of [bootable container](https://github.com/containers/bootc) images shipped via GitHub's container registry. These images are rebuilt daily and pushed to GitHub's container registry. These images are then then pulled in by `rpm-ostree`, which stages updates as a pending deployment for the next boot. To view information about your current local deployments and remotes, run `rpm-ostree status`.

### [Why is Flatpak included? Should I use Flatpak?](#flatpak)
{: #flatpak}

Consult our [Flatpak article](/articles/flatpak).

### [Should I use Electron apps? Why don't they work well with hardened_malloc?](#electron)
{: #electron}

Consult this [discussion](https://github.com/secureblue/secureblue/issues/193#issuecomment-1953323680)

### [My fans are really loud, is this normal?](#fans)
{: #fans}

During rpm-ostree operations, it's normal. Outside of that, make sure you followed the NVIDIA steps in the [post-install instructions](/install#nvidia) if you're using an NVIDIA GPU.

### [Should I use Firejail?](#firejail)
{: #firejail}

[No](https://madaidans-insecurities.github.io/linux.html#firejail), use ``bubblejail`` if there's no Flatpak available for an app.

### [An app I use won't start due to a malloc issue. How do I fix it?](#standard-malloc)
{: #standard-malloc}

- For Flatpaks, remove the `LD_PRELOAD` environment variable via Flatseal. To re-enable hardened_malloc for the respective Flatpak, replace the removed variable.
- For layered packages and packages installed via brew, run the application with `ujust with-standard-malloc APP`. This starts the app without hardened_malloc only once, it does not disable hardened_malloc for the app persistently.

### [On secureblue half of my CPU cores are gone. Why is this?](#smt)
{: #smt}

`mitigations=auto,nosmt` is set on secureblue. This means that if your CPU is vulnerable to attacks that utilize [Simultaneous Multithreading](https://en.wikipedia.org/wiki/Simultaneous_multithreading), SMT will be disabled.

### [How do I install software?](#software)
{: #software}

1. Check if it's already installed using `rpm -qa | grep x`
2. For GUI packages, you can install the Flatpak if available using the Software store or using `flatpak install`. You can browse this [catalogue of Flatpaks](https://flathub.org) to discover the available packages.
3. For CLI packages, you can install from brew if available using `brew install`. You can browse this [catalogue of Homebrew Formulaes](https://formulae.brew.sh) to discover the available formulaes.
4. If a package isn't available via the other two options, or if a package requires greater system integration, `rpm-ostree install` can be used to layer rpms directly into your subsequent deployments.

You can add the unfiltered Flathub repo with `ujust enable-flatpak-unfiltered`.

### [How do I install Steam?](#steam)
{: #steam}

```
ujust install-steam
```

### [How do I enable anti-cheat support?](#anticheat)
{: #anticheat}

{% include alert.html type='note' content='Kernel-level anti-cheat solutions are generally unsupported on desktop Linux.' %}

Anti-cheat solutions typically require process tracing to work - the ability to monitor syscalls (and other signals) from other processes. On Linux, process tracing is controlled by the `kernel.yama.ptrace_scope` kernel parameter. [By default, secureblue doesn't allow ptrace attachment](https://github.com/secureblue/secureblue/blob/605c8cfcd4723fef1e1e4764dcb6870e50514252/files/system/etc/sysctl.d/60-hardening.conf) at all, addressing [basic security concerns](https://www.kernel.org/doc/Documentation/security/Yama.txt). The command below toggles between this restrictive default setting where `ptrace_scope` is set to `3`, breaking anti-cheat software, and a much less restrictive setting where `ptrace_scope` is set to `1`, which allows parent processes to trace child processes, enabling some anti-cheat solutions to work.

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

### [Why am I unable to start containers?](#container-userns)
{: #container-userns}

Software such as Podman and Distrobox need to be able to create user namespaces to work without root. The privilege to do so is denied by default in secureblue, but can be granted by running the following command:

```
ujust toggle-container-domain-userns-creation
```

Trying to start a container without first enabling the ability toggled by the ujust above will result in an `OCI permission denied` error, but beware that enabling it results in a security degradation. Consult our [user namespaces article](/articles/userns) for more details.

### [How do I enable userns for other apps?](#unconfined-userns)
{: #unconfined-userns}

The following command will toggle the ability of processes in the unconfined SELinux domain to create user namespaces. It's necessary for any apps that require this feature, such as bubblewrap when it isn't SUID-root.

```
ujust toggle-unconfined-domain-userns-creation
```

For one example, attempting to bubblewrap a program without first enabling the ability toggled by the ujust above will result in a `bwrap: Creating new namespace failed: Permission denied` error, but beware that enabling it results in a security degradation. Consult our [user namespaces article](/articles/userns) for more details.

### [Another security project has a feature that's missing in secureblue, can you add it?](#feature-request)
{: #feature-request}

First, check our [features list](/features) on whether it already lists an equivalent or better feature. If it doesn't, open a new [GitHub issue](https://github.com/secureblue/secureblue/issues).

### [Why are Bluetooth kernel modules disabled? How do I enable them?](#bluetooth)
{: #bluetooth}

Bluetooth has a long and consistent history of security issues. However, if you still need it, run:

```
ujust toggle-bluetooth-modules
```

### [Why are upgrades so large?](#upgrade-size)
{: #upgrade-size}

This is an issue with rpm-ostree image-based systems generally, and not specific to secureblue. Ideally, upgrades would come in the form of a zstd-compressed container diff, but it's not there yet. Check out [this upstream issue](https://github.com/coreos/rpm-ostree/issues/4012) for more information.

### [Why can't I install new KDE themes?](#ghns)
{: #ghns}

The functionality that provides this, called GHNS, is disabled by default due to the risk posed by the installation of potentially damaging or malicious scripts. This has caused [real damage](https://blog.davidedmundson.co.uk/blog/kde-store-content/).

If you still want to enable this functionality, run:

```
ujust toggle-ghns
```

### [Why doesn't my Xwayland app work?](#xwayland)
{: #xwayland}

Xwayland is disabled by default on GNOME, KDE Plasma, and Sway. If you need it, run:

```
ujust toggle-xwayland
```

### [Why I can't install nor use any GNOME user extensions?](#gnome-extensions)
{: #gnome-extensions}

This is because support for installing & using them has been intentionally disabled by default in secureblue.
Only GNOME system extensions are trusted, if they are installed.

To enable support for installing GNOME user extensions, you can run ujust command:

```
ujust toggle-gnome-extensions
```

### [My clock is wrong, and it's not getting automatically set. How do I fix this?](#clock)
{: #clock}

If your system time is off by an excessive amount due to rare conditions like a CMOS reset, your network will not connect. A one-time manual reset will fix this. This should never be required except under very rare circumstances.

For more technical detail, see [issue #268](https://github.com/secureblue/secureblue/issues/268)

### [How do I get notified of new releases?](#releases)
{: #releases}

To subscribe to release notifications, on the secureblue GitHub page, click "Watch", and then "Custom", and select Releases like so:

<img alt="GitHub screenshot" src="/assets/release-notifications.png" />

### [Why don't my AppImages work?](#appimage)
{: #appimage}

AppImages depend on fuse2, which is unmaintained and depends on a SUID root binary. For this reason, fuse2 support is removed by default. It's strongly recommended that you find alternative mechanisms to install your applications (Flatpak, Distrobox, etc.). If you can't find an alternative and still need fuse2, you can add it back by layering something that depends on it.

For example:

```
rpm-ostree install zfs-fuse
```

### [Why don't KDE Vaults work?](#kde-vaults)
{: #kde-vaults}

Similar to the AppImage FAQ, the KDE Vault default backend `cryfs` depends on fuse2. For this reason, it's recommended that you migrate to an alternative that doesn't depend on fuse2, for example `fscrypt`. If you don't want to do so, you can add fuse2 back by layering something that depends on it, as described in the AppImage FAQ.

### [How do I provision signed Distroboxes?](#distrobox-assemble)
{: #distrobox-assemble}

```
ujust distrobox-assemble
```

### [Why won't Trivalent start when Bubblejailed?](#trivalent-bubblejail)
{: #trivalent-bubblejail}

`bubblejail` shouldn't be used on Trivalent, there are issues reported with the pairing and removing the `bubblejail` config after it is applied can be difficult. It should also be noted that applying additional sandboxing may interfere with chromium's own internal sandbox, so it may end up reducing security.

### [Why won't Trivalent start on Nvidia?](#trivalent-nvidia)
{: #trivalent-nvidia}

On some Nvidia machines, Trivalent defaults to the X11 backend. Since secureblue disables Xwayland by default, this means that you will need to run `ujust toggle-xwayland` and reboot, for Trivalent to work.

### [Why don't some websites that require JIT/WebAssembly work in Trivalent even with the V8 Optimizer toggle enabled?](#trivalent-v8-exceptions)
{: #trivalent-v8-exceptions}

This is an [upstream bug](https://issues.chromium.org/issues/373893056) that prevents V8 optimization settings from being applied to iframes embedded within a parent website. As a result, WebAssembly may not function on services that use a separate URL for their content delivery network or other included domains, such as VSCode Web ([https://github.dev](https://github.dev)). To make VSCode Web work properly, you need to manually allow V8 optimizations for the CDN by adding `https://[*.]vscode-cdn.net` to your list of trusted websites.

### [Why don't extensions work in Trivalent?](#trivalent-extensions)
{: #trivalent-extensions}

Extensions in Trivalent are disabled by default, for security reasons it is not advised to use them. If you want content/ad blocking, that is already built into Trivalent and enabled by default. If you require extensions, you can re-enable them by disabling the `Disable Extensions` toggle under `chrome://settings/security`, then restart your browser (this toggle is per-profile).
\
\
If the extension you installed doesn't work, it is likely because it requires WebAssembly (WASM) for some cryptographic library or some other optimizations (this is the case with the Bitwarden extension). To re-enable JavaScript JIT and WASM for an extension, visit `chrome://extensions`, under the extension with the issues, go `Details -> Site Settings`, then scroll to `V8 Optimizer` and flip to allow.

### [How do I customize secureblue?](#customization)
{: #customization}

If you want to add your own customizations on top of secureblue that go beyond installing packages, you are advised strongly against forking. Instead, create a repo for your own image by using the [BlueBuild template](https://github.com/blue-build/template), then change your `base-image` to a secureblue image. This allows you to apply your customizations to secureblue in a concise and maintainable way, without the need to constantly sync with upstream. For local development, [building locally](/contributing#building-locally) is the recommended approach.

### [How do I add a repo to secureblue?](#adding-repos)
{: #adding-repos}

The process of adding a repository to secureblue is the same as [on Fedora](https://docs.fedoraproject.org/en-US/quick-docs/adding-or-removing-software-repositories-in-fedora/#_for_fedora_41_or_later_dnf_5)

### [How do I install proprietary codecs?](#install-codecs)
{: #install-codecs}

There is no need, they are already included in the image. 

### [How do I change my DE?](#change-de)
{: #change-de}


Choose whatever you like from the [available options](https://secureblue.dev/images) by running `ujust rebase-secureblue`. 

### [Why doesn't DRM content (spotify, netflix etc.) work in Trivalent?](#trivalent-protected-content)
{: #trivalent-protected-content}

DRM-protected content is available in trivalent, however it is disabled by default. Visit `chrome://settings/content/protectedContent` and select "Sites can play protected content"

### [How do I enable kernel modules?](#enable-kernel-modules)
{: #enable-kernel-modules}

Some functionality requires you to enable extra kernel modules that are disabled by default in secureblue. Modules can be enabled by running `ujust override-enable-module`. For instance, mounting SMB shares requires the `cifs` and `netfs` kernel modules. To load them, simply run `ujust override-enable-module cifs` and `ujust override-enable-module netfs` then reboot.
