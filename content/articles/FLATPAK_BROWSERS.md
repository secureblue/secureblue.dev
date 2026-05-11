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
  - [Substituting the sandbox](#sandbox-substituting)
  - [Conclusion](#conclusion)

## [Overview](#overview)
{: #overview}

While we generally encourage Flatpak usage, this is *not* the case for web browsers. Currently, they are incapable of reaching the same level of security within the Flatpak sandbox. This [could change in the future][flatpak-userns], but right now, there are a lot of reasons to be concerned. As such, we consider it safest to avoid them entirely, and we encourage using other methods of installing a browser, assuming a user truly do not want to use Trivalent.

Note that this does not apply as strongly for web-based apps, like those based on Electron. While Electron presents [significant security concerns][electron-concerns], the risks that come from it being flatpaked are not *as* severe as with web browsers. Usually, there is only one page loaded at a time, and that page is chosen by the publisher of the application. This means there is less necessity for sandboxing processes from each other, in comparison to web browsers which are constantly executing untrusted code from a variety of sources. If you keep the Flatpak permissions strict, they are not a catastrophic risk. That said, we primarily encourage [using PWA alternatives][pwa-guide] when possible, as they benefit from Trivalent's hardening and confinement.

We block Flatpak browsers from [appearing in Bazaar][bazaar-blocklist] to discourage downloading them. You could obtain these flatpaks through other methods if you really want to, but we heavily advise that you don't without a very specific reason to do so. We especially encourage you to avoid Firefox and other Gecko-based flatpaks. On top of Firefox's internal sandbox already being [weaker than the Chromium sandbox][firefox], <!-- this link should be replaced with our own article later --> within flatpaks they simply throw up their hands in defeat and [disable a significant part of the internal sandbox][ff-nosandbox]. Technically, the Firefox codebase has a warning about this, however, [they intentionally disable the warning][ff-nowarning] in official flatpaks.

## [Technical details](#technical)
{: #technical}

If you are a general user just wanting to know *what* to do, and you're not interested in the *why*, the rest of this page is not required reading. This is here to document our rationale, to have a consistent resource to link for questions we see often, and simply for those who wish to educate themselves.

### [How browser sandboxing works](#sandboxing-explained)
{: #sandboxing-explained}

Browsers implement their own process-level sandboxing systems, which isolate each loaded page from the system *and* [from each other][site-isolation]. This is a multi-layered system, with different security measures complementing each other. While this article focuses specifically on the [Chromium sandbox][chromium-sandbox] in particular, the principles generally apply to other sandboxing models.

Layer 1 uses [user namespaces][userns], which essentially isolates a process into its own "user", sometimes referred to as a [service account][service-account]. This comes with the usual security benefits of user isolation, such as filesystem permissions and process isolation, while also allowing full control of that environment. This means that a process can have privileged control within the scope of that environment, while being completely unprivileged to anything outside, and having deliberate restrictions on what exactly is exposed within.

Layer 2 uses [`seccomp-bpf`][seccomp], which restricts the system calls that a process is allowed to send to the kernel. For example, applications generally do not need to communicate with device drivers via [`ioctl`][ioctl], especially webapps. Blocking this call removes a lot of attack surface, as an application could otherwise try to exploit a vulnerable driver to escape the sandbox. This idea is expanded to the entire list of system calls, only allowing the [bare minimum needed][least-privilege] to function.

These layers combined form a complete sandbox, restricting what a process can access using user namespaces, and restricting what it can do using `seccomp-bpf`. However, to create this sandbox, a process itself needs the privileges it's restricting, acting as a broker of permissions to its subprocesses. You cannot enforce the law without authority. This is where Flatpak, or really any attempt to sandbox a browser, begins to cause problems. It's essentially placing the broker, and all of the sandboxes it has made, into one big sandbox.

### [How Flatpak's sandbox affects it](#flatpak-sandbox)
{: #flatpak-sandbox}

As of now, Flatpak restricts the ability for applications to create user namespaces [within the Flatpak sandbox][flatpak-userns-block]. This means Layer 1 of the browser sandbox cannot function as intended. Instead, the [`flatpak-spawn`][flatpak-spawn] API must be used as a substitute, essentially asking Flatpak to make a new user namespace for a subprocess it wants to make. However, Flatpak controls this new namespace, not the browser. This makes it difficult to restrict the capabilities of the subprocess beyond what Flatpak already does, weakening Layer 2.

When making a Flatpak package for a browser, a highly specialized part of the internal sandbox has to be replaced with the general purpose Flatpak sandbox. By being general purpose, Flatpak needs to be looser with permissions to allow a variety of things to function. For example, for an app to access the internet, Flatpak needs a hole in the sandbox for packets to go through. However, not every process needs access to this hole, and it's more difficult to granularly restrict what can access it. The browser-specific sandbox is only designed around one application, so it can be finely tuned to exactly what its processes need and no more. For example, it can restrict internet access to a special process for packet traffic, and pass them along to other processes which don't need internet access.

### [Substituting the sandbox](#sandbox-substituting)
{: #sandbox-substituting}

Implementations of this modified sandbox are also of concern. The Chromium team has no involvement in their development, nor are they providing any oversight or guidance. Instead, these are unofficial efforts, and the developers have to keep up with a moving target they don't control, rather than moving along with it as part of Chromium's security model. They inherently have to be extra vigilant that compatibility isn't broken across updates, distracting from being able to strictly prioritize security.

While in some cases the Layer 1 sandbox code is [directly patched][sandbox-patch], most Chromium-based flatpaks utilize a workaround shim called [Zypak][zypak]. It essentially tricks Chromium into believing the offical sandbox is present, then intercepts the calls to create a new user namespace, instead creating new processes with Flatpak's sandbox. Workarounds like this, which the Chromium team isn't accounting for, can lead to unintended behavior and breakage.

These sandbox substitutions also have much smaller teams. Zypak in particular is [maintained by one person][zypak-contribs], which while an impressive undertaking, means they become the sole source of trust for the sandbox functioning properly. Meanwhile, the Chromium sandbox is maintained by an [entire team of professionals][chromium-secteam] who have been involved for years, building off all the [long battle-tested][vuln-lifespan] work done since [2006][chrome-origin], as they [constantly watch][shephard] to identify issues quickly.

Because Chromium is one of the [most used codebases in the world][market-share], and there are countless people, projects, and [governments][cisa-catalog] which rely on it working safely; [many of them][bug-hunters] are independently searching for and [reporting vulnerabilities][chromium-sec-reporting], on top of the formal [security review process][chromium-sec-review] that changes go through. In contrast, these more obscure projects specific to Flatpak have far fewer eyes watching them, and they almost certainly do not have as thorough and strict of a security policy. This lack of scrutiny means they're less understood and less tested, and could be significantly weaker. They may even have major vulnerabilities which nobody has found because nobody has looked, or which have been found yet [haven't been disclosed][zero-days].

### [Conclusion](#conclusion)
{: #conclusion}

It's important to note that none of this is necessarily a flaw in Flatpak. The restriction that causes this problem is part of [what makes Flatpak's sandboxing effective][why-no-userns]. The problem is trying to sandbox an application which uses the same tools to sandbox itself. Flatpak is designed to accomodate applications which don't expect a restricted environment, and aren't concerned with creating a restricted environment. For that purpose, it can be very effective. But when an application is already designed with security in mind, using its own privilege to restrict itself, taking away that privilege leads to significant challenges.

These are not exclusively our concerns, they are shared by a number of others, including the [Vivaldi developers][vivaldi], the [Brave developers][brave], the [Helium developers][helium], the [Cromite developers][cromite], the [Tails developers][tails], and even the [Chromium developers themselves][chromium-team-response]. A number of browser projects are wary of making a Flatpak package, because it would mean officially endorsing this method of sandboxing.

Until Flatpak has the necessary features to allow internal browser sandboxes to function as intended, we will continue to strongly recommend against usage of flatpaked browsers. The Flatpak maintainers have stated this is something they're working on, but it will take time for it to be implemented, and for it to be adopted by browsers. While we heavily recommend using Trivalent, and many of our efforts revolve around it; we cannot stop users from installing another browser, so it is important to inform them there are much safer methods available than Flatpak as it is now.

[flatpak-userns]: https://github.com/imputnet/helium-linux/issues/46#issuecomment-3707223527
[electron-concerns]: https://github.com/secureblue/secureblue/issues/193#issuecomment-1953323680
[pwa-guide]: https://support.google.com/chrome/answer/9658361
[bazaar-blocklist]: https://github.com/secureblue/secureblue/tree/live/files/system/desktop/usr/share/bazaar
[firefox]: https://madaidans-insecurities.github.io/firefox-chromium.html
[ff-nosandbox]: https://bugzilla.mozilla.org/show_bug.cgi?id=1756236
[ff-nowarning]: https://hg-edge.mozilla.org/releases/mozilla-beta/rev/509d4746f2d6
[site-isolation]: https://www.chromium.org/Home/chromium-security/site-isolation/
[chromium-sandbox]: https://chromium.googlesource.com/chromium/src/+/0e94f26e8/docs/linux_sandboxing.md
[userns]: https://lwn.net/Articles/531114/
[service-account]: https://unix.stackexchange.com/questions/314725/what-is-the-difference-between-user-and-service-account
[seccomp]: https://blog.chromium.org/2012/11/a-safer-playground-for-your-linux-and.html
[ioctl]: https://en.wikipedia.org/wiki/Ioctl
[least-privilege]: https://en.wikipedia.org/wiki/Principle_of_least_privilege
[flatpak-userns-block]: https://github.com/flatpak/flatpak/blob/1.17.7/common/flatpak-run.c#L2349
[flatpak-spawn]: https://docs.flatpak.org/en/latest/flatpak-command-reference.html#flatpak-spawn
[sandbox-patch]: https://github.com/flathub/org.chromium.Chromium/blob/master/patches/chromium/flatpak-Add-initial-sandbox-support.patch
[zypak]: https://github.com/refi64/zypak
[zypak-contribs]: https://github.com/refi64/zypak/graphs/contributors
[chromium-secteam]: https://www.chromium.org/Home/chromium-security/brag-sheet/
[vuln-lifespan]: https://security.googleblog.com/2024/09/eliminating-memory-safety-vulnerabilities-Android.html
[chrome-origin]: https://blog.chromium.org/2018/09/how-we-designed-chrome-10-years-ago.html
[shephard]: https://chromium.googlesource.com/chromium/src/+/HEAD/docs/security/shepherd.md
[market-share]: https://gs.statcounter.com/browser-market-share
[cisa-catalog]: https://www.cisa.gov/known-exploited-vulnerabilities-catalog "Change the Vendor filter to Google"
[bug-hunters]: https://bughunters.google.com/leaderboard
[chromium-sec-reporting]: https://www.chromium.org/Home/chromium-security/reporting-security-bugs/
[chromium-sec-review]: https://www.chromium.org/Home/chromium-security/security-reviews/
[zero-days]: https://www.vice.com/en/article/zero-day-exploits-rarely-discovered-by-more-than-one-group-study-finds/
[why-no-userns]: https://github.com/flatpak/flatpak/issues/5879#issuecomment-2255568180

[vivaldi]: https://forum.vivaldi.net/topic/33411/flatpak-support/191
[brave]: https://brave.com/linux/#:~:text=it%20modifies%20Chromium%20sandboxing%20in%20ways%20which%20have%20not%20been%20vetted%20by%20the%20Brave%20or%20Chromium%20security%20teams
[helium]: https://github.com/imputnet/helium-linux/issues/46#issuecomment-3735501507
[cromite]: https://github.com/uazo/cromite/issues/1053#issuecomment-2191794660
[tails]: https://gitlab.tails.boum.org/tails/tails/-/merge_requests/1025#note_253065
[chromium-team-response]: https://issues.chromium.org/issues/40753165#comment11
