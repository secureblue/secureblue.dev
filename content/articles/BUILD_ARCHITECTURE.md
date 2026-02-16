---
title: "Build Architecture | secureblue"
description: "Build architecture for secureblue"
permalink: /articles/build-architecture
---

# Build architecture

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Introduction](#introduction)
- [Definitions](#definitions)
- [Mitigation logic](#mitigation-logic)
  - [Provenance](#provenance)
  - [Signatures](#signatures)
  - [Egress auditing](#egress-auditing)
  - [Branch protection](#branch-protection)
- [Build rocess](#build-process)

## [Introduction](#introduction)
{: #introduction}

Supply chain security is a priority for secureblue. During the the build process, we use complementary security mechanisms to protect against a variety of supply chain attack vectors. The documentation below covers each of these mechanisms, the protections they provide, and where secureblue uses these mechanisms.

## [Definitions](#definitions)
{: #definitions}

| Security mechanism  | Implementation tooling | Threat vectors | Scope   |
|------------|---------------------------------------|-------------------------|--------------|---------------------------------|
| Provenance      | [SLSA](https://slsa.dev)                                   | Maintainer signing key theft, Rogue maintainers | All secureblue [OCI](https://opencontainers.org/) images, Trivalent RPM packages, Blue-Build build tools |
| Signatures | [cosign](https://github.com/sigstore/cosign), [GPG](https://gnupg.org/) | Artifact tampering, Artifact forgery, Registry credential theft | All secureblue [OCI](https://opencontainers.org/) images, all secureblue ISOs and torrents, all secureblue RPM packages, all Fedora RPM packages, all Flatpaks from Flathub ([centrally signed](https://flathub.org/repo/flathub.gpg)), Blue-Build build tools |
| Egress auditing | [StepSecurity Harden-Runner](https://docs.stepsecurity.io/harden-runner) | Maintainer secrets exfiltration, Source code tampering, Dependency tampering, Registry credential theft | All secureblue OCI image builds, Trivalent RPM builds |
| Branch protection | [GitHub Rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) | Maintainer source code repository credential theft, Rogue maintainers | All secureblue source code repositories |

## [Mitigation logic](#mitigation-logic)
{: #mitigation-logic}

### [Provenance](#provenance)
{: #provenance}

To generate provenance, the build platform (in our case, [GitHub Actions](https://github.com/features/actions)) generates and signs an attestation file containing metadata about the build environment. Crucially, it cryptographically attests to the authenticity of runner and the source commit on which the artifact is being built. This attestation is then published in the repository or registry alongside the artifact. On the client side, when the artifact is pulled, the signature of the attestation is [validated](https://github.com/slsa-framework/slsa-verifier) against the build platform's public key and the contents of the attestation are validated to confirm that the artifact was built: on an authorized runner from a commit in a specific branch in the source repository (in our case, protected by branch policies, pull request review, and maintainer login 2FA). This means that even in the event that a maintainer's artifact signing keys and artifact repository credentials were both stolen, any malicious builds pushed by the credential thief would be rejected by clients due to provenance validation.

### [Signatures](#signatures)
{: #signatures}

A private key owned by the artifact maintainer is used in combination with a [hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) of the artifact to compute a [signature](https://en.wikipedia.org/wiki/Digital_signature). The signature is then provided alongside the artifact so that clients can verify the artifact signature before installing or using the artifact. For example, for our ISOs, each signature is shipped in a corresponding `-CHECKSUM` file. Once the client has all of the required information, it can use the maintainer's public key to verify the signature, revealing a hash that it then compares against a locally-generated hash of the artifact. This means that in the event that an artifact registry was compromised or artifacts otherwise tampered with by malicious third parties, any corresponding signature file would either not be present or fail validation.

### [Egress auditing](#egress-auditing)
{: #egress-auditing}

StepSecurity [Harden-Runner](https://docs.stepsecurity.io/harden-runner) provides network traffic controls and source code integrity monitoring, among other mechanisms. It restricts outbound traffic to a configurable list of authorized outbound domains, and enforces this at multiple levels (DNS, HTTPS, network layer, transport layer). It has several other functions as well, like  monitoring the source code as the build progresses to ensure tampering doesn't occur, monitoring for anomalous privileged processes, etc.

### [Branch protection](#branch-protection)
{: #branch-protection}

Branch protection via [rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) prevents any changes being made to secureblue source code without those changes first meeting specific criteria. Among those criteria is a minimum number of code reviews from maintainers, excluding of course the author of the pull request should they be a maintainer. This means that in the event that a maintainer's source code repository credentials were stolen, the thief would be unable to push changes to the repository. This includes the repo owner credentials, since bypassing rulesets is only possible after 2FA has been granted.

## [Build process](#build-process)
{: #build-process}

<a href="/assets/architecture.png" target="_blank">
  <img src="/assets/architecture.png" alt="Secureblue Architecture">
</a>
