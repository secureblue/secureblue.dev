---
title: "Install | secureblue"
description: "Steps to install secureblue"
permalink: /install
---

# Install

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Pre-install](#pre-install)
- [Terms of Use](#terms)
- [Installation](#installation)
  - [Secureblue ISO (Desktop)](#iso)
    - [ISO Verification](#verification)
  - [Ignition (Server)](#ignition)
  - [Rebase (IOT)](#rebase)
  - [Rebase (ARM64 - Beta)](#arm64)
- [Post-install](#post-install)

<hr>

## [Pre-install](#pre-install)
{: #pre-install}

{% include alert.html type='note' content='The cross-platform Fedora Media Writer is the <em>official, tested, and supported</em> method for the creation of bootable media. Instructions are available in the <a href="https://docs.fedoraproject.org/en-US/fedora/latest/preparing-boot-media/">Fedora documentation</a>. Do <a href="https://github.com/ventoy/Ventoy/issues/2795">not</a> use Ventoy.' %}

Before installation, the following checks are recommended:

- Ensure SecureBoot is enabled.
- Ensure your BIOS is up-to-date by checking its manufacturer's website.
- Set a BIOS password to prevent tampering.

<hr>

## [Terms of Use](#terms)
{: #terms}

secureblue includes a combination of software packages, each under its own licensing terms. The license of secureblue is the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). The license of secureblue does not supersede the licenses of upstream code and content contained in secureblue images. By downloading secureblue you agree to the license terms of its use.

```
Copyright 2024-2025 The Secureblue Authors

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this software except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

<hr>

## [Installation](#installation)
{: #installation}

To install secureblue, you will use one of the following processes. Consult the table below for the right starting point for your use case. For more details on the available images, have a look at the <a href="/images">list of available images</a> before proceeding.

| Image Type | Installation Process                                                                           | Recommended Use Cases          |
|------------|------------------------------------------------------------------------------------------------|--------------------------------|
| Desktop    | Direct installation with a [secureblue ISO](#iso)                                              | Desktop/laptop end user        |
| CoreOS     | Installation using [Ignition via Butane](#ignition).                                           | Cloud, containerized workloads |
| IOT        | Installation via [rebase](#rebase).                                                            | Edge computing, bare-metal     |

Things to remember during installation:

- Select the option to encrypt the drive you're installing to.
- Use a [strong password](https://www.eff.org/dice) when prompted.
- Select wheel group membership for your user when prompted.

### [Secureblue ISO (Desktop)](#iso)
{: #iso}

{% include alert.html type='note' content='<b>nvidia-open</b> images are recommended for systems with NVIDIA GPUs Turing or newer (GTX 16XX+, RTX 20XX+). Consult <a href="https://nouveau.freedesktop.org/CodeNames.html">this page</a> if you&#39;re not sure what family your GPU belongs to. These include the new <a href="https://github.com/NVIDIA/open-gpu-kernel-modules">open kernel modules</a> from NVIDIA, not Nouveau. <b>nvidia</b> images are recommended for systems with NVIDIA GPUs Pascal or older. These include the closed kernel modules from NVIDIA.' %}

<form method="GET">
  <select name="de" id="de" required>
    <option value="">Select an environment</option>
    <option value="silverblue">GNOME</option>
    <option value="kinoite">KDE Plasma</option>
    <option value="sericea">Sway</option>
  </select>
  <select name="nvidia" id="nvidia" required>
    <option value="">Do you have NVIDIA?</option>
    <option value="main">No</option>
    <option value="nvidia">Yes (pre-Turing)</option>
    <option value="nvidia-open">Yes (Turing and later)</option>
  </select>
  <br><br>
  <label>
    <input type="checkbox" name="terms" required>
    I have read and agree to the <a href="#terms">Terms of Use</a>
  </label>
  <br><br>
  <i>Download options</i><br />
  <button type="submit" formaction="https://isos.secureblue.dev/download">Download ISO</button>
  <button type="submit" formaction="https://isos.secureblue.dev/downloadTorrent" formtarget="_blank" >Download torrent</button>
  <br /><br />
  <i>Verification files</i><br />
  <button type="submit" formaction="https://isos.secureblue.dev/downloadSHA256SUM">Download ISO checksum</button>
  <button type="submit" formaction="https://isos.secureblue.dev/downloadTorrentSHA256SUM">Download torrent checksum</button>
  <a href="https://isos.secureblue.dev/secureblue-keyring.gpg" class="button">Download&nbsp;keyring</a>
  <br /><br />
</form>

#### [ISO Verification](#verification)
{: #verification}

Verify the secureblue installation media before proceeding: <a href="/verification" target="_blank" class="button">Verification</a>

### [Ignition (CoreOS)](#ignition)
{: #ignition}

Follow the [Fedora CoreOS docs](https://docs.fedoraproject.org/en-US/fedora-coreos/getting-started/), [Ignition docs](https://docs.fedoraproject.org/en-US/fedora-coreos/producing-ign/), and [Butane docs](https://coreos.github.io/butane/) to configure initialization for your CoreOS instance(s).

You can use our [example.butane](https://github.com/secureblue/secureblue/blob/live/docs/example.butane) as a starting point.

### [Rebase (IOT)](#rebase)
{: #rebase}

Install Fedora IOT using one of the [official methods](https://fedoraproject.org/iot/download).

Once Fedora IOT is installed, rebase to secureblue by selecting an appropriate image from [this list](https://github.com/orgs/secureblue/packages?tab=packages&q=iot), and then running the following command:

```
sudo bootc switch ghcr.io/secureblue/${IMAGE_NAME}:latest
```

### [Rebase (ARM64 - Beta)](#arm64)
{: #arm64}

Some of our images have Beta support for the ARM64 / aarch64 architecture. Consult the <a href="/images">list of available images</a> to check which images have aarch64 support. For images with aarch64 support, our image manifests are multiarch. This means that to install a secureblue aarch64 image, simply install Fedora Atomic using a Fedora Atomic ISO, and then rebase using `bootc` like so:

```
sudo bootc switch ghcr.io/secureblue/${IMAGE_NAME}:latest
```

<hr>

## [Post-install](#post-install)
{: #post-install}

Finish setting up your secureblue installation: <a href="/post-install" target="_blank" class="button">Post-install steps</a>
