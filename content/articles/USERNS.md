---
title: "User namespaces | secureblue"
description: "Brief explanation of unprivileged user namespaces and how the feature is handled in secureblue"
permalink: /articles/userns
---

# User namespaces

[User namespaces](https://en.wikipedia.org/wiki/Linux_namespaces#User_ID_(user)) (userns) are a kernel feature introduced in kernel version 3.8 that can be useful for sandboxing but also exposes a lot of kernel attack surface. When an unprivileged user asks the kernel to create a namespace, the kernel needs to permit that user to do so. Whether this is permitted by the kernel can be controlled via a sysctl flag, or in a more fine-grained way by Linux security modules like SELinux or AppArmor.

There is a [long history](https://madaidans-insecurities.github.io/linux.html#kernel) of vulnerabilities made possible by allowing this functionality for unprivileged users ever since its [introduction](https://gitlab.com/apparmor/apparmor/-/wikis/unprivileged_userns_restriction). The main issue is that user namespaces allow unprivileged users to interact with large areas of kernel code that require elevated [capabilities](https://www.man7.org/linux/man-pages/man7/capabilities.7.html), which would otherwise only be accessible to privileged users.

(Canonical considers user namespaces to be a substantial risk too, and has restricted them via a global AppArmor policy [since 23.10 by opt-in](https://discourse.ubuntu.com/t/spec-unprivileged-user-namespace-restrictions-via-apparmor-in-ubuntu-23-10/37626) and [since 24.04 by default](https://ubuntu.com/blog/whats-new-in-security-for-ubuntu-24-04-lts).)

Given this history and attack surface, you might think we should just disable this functionality altogether. However, if unprivileged user namespaces are disabled globally, then programs like Trivalent and Flatpak can't function. To mitigate this, we apply an SELinux policy that denies all but a short list of SELinux domains permission to create user namespaces. The domains that are allowed by default to create user namespaces include Trivalent, Flatpak, and certain system services.

We also replace [Bubblewrap](https://github.com/containers/bubblewrap) with a patched version that only allows the root user to create user namespaces with elevated capabilities, denying that large attack surface while still allowing it to be used as a sandboxing tool, and confine Bubblewrap with an SELinux policy that is also allowed to create user namespaces by default. We also install Bubblejail, a sandboxing tool that uses Bubblewrap.

We don't allow Podman or other container-domain programs the same permission by default, because they can be used to create user namespaces with arbitrary capabilities and therefore potentially expose similar attack surface. If you need container functionality (e.g., for Podman, Docker, or Distrobox), you can enable it with `ujust set-container-userns on`.

If you need to use any other software that requires user namespace creation privileges (e.g. Electron apps not installed as a Flatpak app), you can enable it with `ujust set-unconfined-userns on`. However, keep in mind that this is a security degradation.
