---
title: "User Namespaces | secureblue"
description: "Brief explanation of unprivileged user namespaces and how the feature is handled in secureblue"
permalink: /articles/userns
---

# User namespaces

[User namespaces](https://en.wikipedia.org/wiki/Linux_namespaces#User_ID_(user)) are a kernel feature introduced in kernel version 3.8. When an unprivileged user asks the kernel to create a namespace, the kernel needs to permit that user to do so. Whether this is permitted by the kernel is controlled via a sysctl flag.

There is a [long history](https://madaidans-insecurities.github.io/linux.html#kernel) of vulnerabilities made possible by allowing this functionality for unprivileged users ever since its [introduction](https://gitlab.com/apparmor/apparmor/-/wikis/unprivileged_userns_restriction). Given this history, you might think we should just disable this functionality altogether. However, if this functionality is disabled globally, then flatpak can't function.

To mitigate this, we first revoke user_namespace privileges from the unconfined domain in our SELinux policy. Then, we confine flatpak and grant it user_namespace privileges. We do the same for Trivalent. This allows them to create user namespaces while keeping them globally disabled by default. We don't do this for Bubblewrap or Podman directly because the syscall filters for both are weak by default. If you need container domain userns (e.g., for Distrobox), you can enable it with `ujust toggle-container-domain-userns-creation`. If you need to use any other software that requires user namespace creation privileges (e.g., Bubblejail), you can enable it with `ujust toggle-unconfined-domain-userns-creation`. But keep in mind that this is a security degradation.

Canonical considers user namespaces to be a substantial risk, too, and has restricted them via a global AppArmor policy [since 23.10 by opt-in](https://discourse.ubuntu.com/t/spec-unprivileged-user-namespace-restrictions-via-apparmor-in-ubuntu-23-10/37626) and [since 24.04 by default](https://ubuntu.com/blog/whats-new-in-security-for-ubuntu-24-04-lts).
