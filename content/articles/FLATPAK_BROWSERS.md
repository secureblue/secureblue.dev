---
title: "Flatpak web browsers | secureblue"
description: "The struggles of keeping the web sandboxed within a sandbox"
permalink: /articles/flatpak-browsers
---

# Flatpak web browsers

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Overview](#overview)
- [Technical details](#technical)
  - [How browser sandboxing works](#sandboxing-explained)
  - [How Flatpak's sandbox affects it](#flatpak-sandbox)
  - [Replacing the sandbox](#sandbox-replacing)
  - [Conclusion](#conclusion)

## [Overview](#overview)
{: #overview}

While we generally encourage Flatpak usage, this is <em>not</em> the case for web browsers. Currently, they are incapable of reaching the same level of security within the Flatpak sandbox. This [could change in the future](https://github.com/flatpak/flatpak/pull/6386), but right now, there are a lot of reasons to be concerned. As such, we consider it safest to avoid them entirely, and we encourage using other methods of installing a browser, assuming a user truly do not want to use Trivalent.

Note that this does not apply as strongly for web-based apps, like those based on Electron. While they also present [significant security concerns](https://github.com/secureblue/secureblue/issues/193#issuecomment-1953323680), the risk is not *as* severe. Usually, there is only one page loaded at a time, and that page is chosen by the publisher of the application. This means there is less necessity for sandboxing processes from each other, in comparison to web browsers which are constantly executing untrusted code from a variety of sources. If you keep the Flatpak permissions strict, they are not a catastrophic risk. That said, we primarily encourage [using PWA alternatives](https://support.google.com/chrome/answer/9658361) when possible, as they benefit from Trivalent's hardening and confinement.

We also block Flatpak browsers from [appearing in Bazaar](https://github.com/secureblue/secureblue/tree/live/files/system/desktop/usr/share/bazaar) to discourage downloading them. You could obtain these flatpaks through other methods if you really want to, but we heavily advise that you don't without a very specific reason to do so. We especially encourage you to avoid Firefox and other Gecko-based flatpaks, as they simply throw up their hands in defeat and [disable a significant part of the internal sandbox](https://bugzilla.mozilla.org/show_bug.cgi?id=1756236). Technically, the Firefox codebase has a warning about this, however, [they intentionally disable the warning](https://hg-edge.mozilla.org/releases/mozilla-beta/rev/509d4746f2d6) in the official Flatpak.

## [Technical details](#technical)
{: #technical}

If you are a general user just wanting to know *what* to do, and you're not interested in the *why*, the rest of this page is not required reading. This is here to document our rationale, to have a consistent resource to link for questions we see often, and simply for those who wish to educate themselves. 

### [How browser sandboxing works](#sandboxing-explained)
{: #sandboxing-explained}

Browsers implement their own process-level sandboxing systems, which isolate each loaded page from the system *and* from each other. While this article focuses specifically on the [Chromium sandbox](https://chromium.googlesource.com/chromium/src/+/0e94f26e8/docs/linux_sandboxing.md) in particular, the principles generally apply to other sandboxing models. Chromium's internal sandbox is comprised of multiple layers.

Layer 1 uses [user namespaces](https://lwn.net/Articles/531114/), which essentially isolates a process into its own "user". This comes with the usual security benefits of user isolation, such as filesystem permissions and process isolation, while also allowing full control of that environment. This means that a process can have privileged control within the scope of that environment, while being completely unprivileged to anything outside, and having deliberate restrictions on what exactly is exposed within.

That leads to Layer 2, which utilizes [`seccomp-bpf`](https://blog.chromium.org/2012/11/a-safer-playground-for-your-linux-and.html). This restricts the system calls that a process is allowed to send to the kernel. For example, applications often do not need to communicate with device drivers via [`ioctl`](https://en.wikipedia.org/wiki/Ioctl), especially websites. Blocking this call removes a lot of attack surface, as an application could try to exploit a vulnerable driver to escape the sandbox. This idea is expanded to the entire list of system calls, only allowing the [bare minimum needed](https://en.wikipedia.org/wiki/Principle_of_least_privilege) to function.

These layers combined form a complete sandbox, restricting both what a process can access, and what it can do. However, to create this sandbox, a process needs those privileges itself, acting as a broker of permissions to its subprocesses. You cannot enforce the law without authority. This is where Flatpak, or really any attempt to sandbox a browser, begins to cause problems. It's essentially placing the broker, and all of the sandboxes it has made, into one big sandbox.

### [How Flatpak's sandbox affects it](#flatpak-sandbox)
{: #flatpak-sandbox}

As of now, Flatpak restricts the ability for applications to create user namespaces [within the Flatpak sandbox](https://github.com/flatpak/flatpak/blob/6dfe1ad4d0a4c0bd9b1c44294aeeac903b0d3bf4/common/flatpak-run.c#L2349). This means Layer 1 of the browser sandbox cannot function. To recreate it, the browser must instead use the [`flatpak-spawn`](https://docs.flatpak.org/en/latest/flatpak-command-reference.html#flatpak-spawn) API, essentially asking Flatpak to make a new user namespace for a subprocess it wants to make. However, Flatpak controls this new namespace, not the browser. This makes it difficult to restrict the capabilities of the subprocess beyond what Flatpak already does, weakening Layer 2.

Essentially, by making a browser a flatpak, the sandbox designed for the browser has to be replaced with the general purpose Flatpak sandbox. By being general purpose, Flatpak needs to be looser with permissions to allow a variety of things to function. For example, for an app to access the internet, Flatpak needs a hole in the sandbox for packets to go through. But this hole is open for the entire sandbox, even if certain things don't need access to it. The browser-specific sandbox can be finely tuned to exactly what its processes need and no more. It can restrict internet access to a special process for packet traffic, and pass them along to other processes which don't need internet access.

### [Replacing the sandbox](#sandbox-replacing)
{: #sandbox-replacing}

Implementations of this sandbox replacement is also of concern, as the Chromium team has no involvement in them whatsoever. The people most familiar with Chromium's security model do not develop them and have no oversight over them.

While sometimes the sandboxing source code is [directly patched](https://github.com/flathub/org.chromium.Chromium/blob/master/patches/chromium/flatpak-Add-initial-sandbox-support.patch), Chromium-based flatpaks typically utilize a workaround shim called [Zypak](https://github.com/refi64/zypak). It essentially tricks Chromium into believing the offical sandbox is present, then intercepts the calls to create a new user namespace, instead creating new processes with Flatpak's sandbox. Workarounds like this, which Chromium has no idea are even happening, mean we're dealing with unintended behavior that the Chromium team isn't accounting for.

Additionally, these attempts to reimplement the sandbox are much more obscure projects, with no formal security auditing, and far less eyes watching them. This lack of scrutiny means they're less understood and less tested, and could be significantly weaker, perhaps even having major vulnerabilities which nobody has noticed because nobody has looked. Zypak in particular is [maintained by one person](https://github.com/refi64/zypak/graphs/contributors), which while an impressive undertaking, means they are the sole source of trust for the sandbox.

Meanwhile, the Chromium sandbox is maintained by an entire team of professionals who have been involved for years, building off all the [long battle-tested](https://security.googleblog.com/2024/09/eliminating-memory-safety-vulnerabilities-Android.html) work done since [2006](https://blog.chromium.org/2018/09/how-we-designed-chrome-10-years-ago.html), as their work is watched by the countless projects which rely on it. Chromium is easily one of the most used codebases in the world, and countless individuals, businesses, and governments have a lot of stake in making sure it works safely.

### [Conclusion](#conclusion)
{: #conclusion}

None of this is necessarily a flaw in Flatpak. The restrictions causing these problems are exactly [what makes its sandboxing effective](https://github.com/flatpak/flatpak/issues/5879#issuecomment-2255568180). The problem is trying to sandbox a sandbox. Flatpak is designed to sandbox applications which are designed to be privileged already. For that purpose, it can work quite well, which is why we use and encourage usage of it. But when an application is already designed with security in mind, using its own privilege to restrict itself, taking away that privilege leads to significant challenges.

While we don't want to make this an appeal to authority argument, these concerns don't just come from us, they have also been raised by the [Vivaldi developers](https://forum.vivaldi.net/topic/33411/flatpak-support/191), the [Brave developers](https://brave.com/linux/#:~:text=it%20modifies%20Chromium%20sandboxing%20in%20ways%20which%20have%20not%20been%20vetted%20by%20the%20Brave%20or%20Chromium%20security%20teams), the [Helium developers](https://github.com/imputnet/helium-linux/issues/46#issuecomment-3735501507), the [Cromite developers](https://github.com/uazo/cromite/issues/1053#issuecomment-2191794660), the [Tails developers](https://gitlab.tails.boum.org/tails/tails/-/merge_requests/1025#note_253065), and even the [Chromium developers themselves](https://issues.chromium.org/issues/40753165#comment11). Many projects have expressed interest in making a Flatpak package, and would see benefit from doing so, but are wary of officially endorsing this method of sandboxing.

As such, until Flatpak has the necessary features to allow proper internal sandboxing, and browsers begin *officially* implementing it, we will continue to strongly recommend against usage of browsers installed via Flatpak. While we will always primarily recommend using Trivalent, especially since much of secureblue's hardening efforts revolve around it; there are valid reasons to want to install another browser, and we want to make sure that's done in the safest manner available. Unfortunately, that is not Flatpak.
