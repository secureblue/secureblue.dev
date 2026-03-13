---
title: "Flatpak | secureblue"
description: "Flatpak: the good, the bad, the ugly"
permalink: /articles/flatpak
---

# Flatpak

Flatpak is an application packaging and distribution system for desktop Linux. It uses Bubblewrap under the hood to sandbox those applications and provide desktop Linux with a de facto standard sandboxing and permissions system. However, it has flaws and its sandboxing strength can vary significantly depending on how it is configured. secureblue addresses these flaws in a couple different ways.

As with any application sandboxing system, flatpaks should be scoped down by default to as few permissions as they need to function. Even better, permissions should be granted directly by the user at app runtime, like in Android. Sadly, neither of these are the case today. Flatpak manifest maintainers define the set of permissions they believe to be necessary and sufficient for the operation of their applications. When a flatpak is installed by a user, the flatpak's permissions default to those defined by the manifest.

This is of course not ideal, but it's also [not a reason to abandon flatpak entirely](https://en.wikipedia.org/wiki/Perfect_is_the_enemy_of_good). There are many ways this issue can be mitigated, with different degrees of difficulty:

- Users can configure permissions to their liking.
- Users can submit issues and/or PRs to make changes to the default permissions for specific flatpaks.
- Developers can, over the long term, improve flatpak and xdg portals to introduce a more robust permissions model.

What secureblue does is provide a mitigation along the lines of the first option. We provide a `ujust` command to strip flatpaks of permissions by default, such that the user will need to specifically and deliberately grant permissions required by each application:

```
ujust flatpak-permissions-lockdown
```

This is not enabled out of the box on secureblue because it has a somewhat significant usability impact (many flatpaks will break due to missing permissions). Until the flatpak and xdg portal permissions model is improved, this is the most secure option we can offer. That said, users are still encouraged to report unnecessary permissions to upstream projects when found, while incremental development progresses on flatpak and portals.

You can revert this change by running:

```
ujust flatpak-reset-global-overrides
```

Note that this will not only undo the `ujust flatpak-permissions-lockdown` command but also any other global overrides (individual app overrides will not be affected). This also affects flatpak's hardened_malloc integration (which is set by default), so you should run this afterwards:

```
ujust harden-flatpak
```
Part of flatpak's security model involves [denying user namespaces](https://forum.vivaldi.net/topic/33411/flatpak-support/191) [via SECCOMP-BPF](https://www.kernel.org/doc/html/v4.19/userspace-api/seccomp_filter.html) [to flatpaks, including flatpak browsers](https://discussion.fedoraproject.org/t/is-it-better-to-have-a-browser-sand-boxed-with-flatpak-or-not/162425/17). This is one way to isolate them from the system & other apps by preventing them from reaching certain kernel code paths that they would otherwise not be able to as unprivileged processes but breaks their sandboxing layer responsible for site and process isolation, leaving only [Zypak](https://github.com/refi64/zypak) + SECCOMP-BPF + [additional patches](https://github.com/flathub/org.chromium.Chromium/blob/master/patches/chromium/flatpak-Add-initial-sandbox-support.patch) in its place; or in the case of Firefox/Gecko-based browsers, [outright disables most sandboxing processes entirely by having no Zypak equivalent](https://bugzilla.mozilla.org/show_bug.cgi?id=1756236). The sole known exception to this being the GNOME Web/Epiphany flatpak, whose site isolation is [notably weaker](https://github.com/RKNF404/chromium-hardening-guide/blob/main/pages/BROWSER_SELECTION.md#epiphanywebkitgtk) in comparison to the implementations in native Chromium-based & Gecko-based browser packages.

Therefore we've kept hidden flatpak browsers (aside from GNOME Web) from view on the software store frontends including [recently](https://github.com/secureblue/secureblue/pull/1898) on Bazaar to discourage their usage and directing the user to Trivalent.

We have interest in shipping Trivalent as a flatpak in the future nonetheless for more availability but not until a solution like [nested namespaces](https://github.com/flatpak/flatpak/pull/6386) becomes usable to allow the browser to perform the necessary syscalls to execute its sandboxed processes properly.
