---
title: "Verification | secureblue"
description: "Steps to verify secureblue media"
permalink: /verification
---

# Verification

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Prerequisites](#prerequisites)
  - [Windows](#windows)
  - [macOS](#macOS)
  - [Linux](#linux)
<!-- - [For torrent users](#torrent-users) -->
- [For all users](#all-users)

<hr>

## [Prerequisites](#prerequisites)
{: #prerequisites}

Verifying secureblue media requires both the `gpgv` and `sha256sum` commands.

### [Windows](#windows)
{: #windows}

Open PowerShell as a regular user to run these commands:

```
winget install -e --id GnuPG.GnuPG
winget install -e --id uutils.coreutils
```

To temporarily enable the `sha256sum` command, you must then **restart PowerShell** and run:

```
function sha256sum {
    coreutils.exe sha256sum @args
}
```

### [macOS](#macOS)
{: #macOS}

In the terminal, install [Homebrew](https://brew.sh/) if you haven't already. Then:

```
brew install gnupg coreutils
```

To temporarily enable the `sha256sum` command, run:

```
export PATH="$(brew --prefix coreutils)/libexec/gnubin:$PATH"
```

### [Linux](#linux)
{: #linux}

If `gpg --version` gives a `command not found` error, then you do not have GPG installed. You can get this via your distribution's package manager, or by installing [Homebrew](https://brew.sh/), then:

```
brew install gnupg
```

<!-- ## [For torrent users](#torrent-users)
{: #torrent-users}

At this point, you should have the torrent file, its corresponding CHECKSUM file, and the keyring file. Use following commands to verify the ISO (where `${IMAGE_NAME}` corresponds to the filename of the torrent you downloaded).

First command:

```
gpgv --keyring ./secureblue-keyring.gpg "${IMAGE_NAME}.iso.torrent-CHECKSUM"
```

Expected output:

```
gpgv: Signature made Wed 04 Jun 2025 12:49:39 AM PDT
gpgv:                using EDDSA key 26B4463ED8F313BC7E3FBDF9D9223AF0F47B3E41
gpgv: Good signature from "secureblueadmin <secureblueadmin@proton.me>"
```

{% include alert.html type='note' content='The signature date in the top line may vary depending on how recent your ISO is, and your local time zone. What matters is that gpgv says the checksum\'s signature is good and matches the keyring.' %}

Second command:

```
sha256sum -c "${IMAGE_NAME}.iso.torrent-CHECKSUM"
```

Expected output:

```
IMAGE_NAME.iso.torrent: OK
sha256sum: WARNING: 8 lines are improperly formatted
```

## [For all users](#all-users)
{: #all-users}

At this point, you should have the ISO, its corresponding CHECKSUM file, and the keyring file. Use following commands to verify the ISO (where `${IMAGE_NAME}` corresponds to the filename of the ISO you downloaded).

First command:

```
gpgv --keyring ./secureblue-keyring.gpg "${IMAGE_NAME}.iso-CHECKSUM"
```

Expected output:

```
gpgv: Signature made Wed 04 Jun 2025 12:49:39 AM PDT
gpgv:                using EDDSA key 26B4463ED8F313BC7E3FBDF9D9223AF0F47B3E41
gpgv: Good signature from "secureblueadmin <secureblueadmin@proton.me>"
```

{% include alert.html type='note' content='The signature date in the top line may vary depending on how recent your ISO is, and your local time zone. What matters is that gpgv says the checksum\'s signature is good and matches the keyring.' %}

Second command:

```
sha256sum -c "${IMAGE_NAME}.iso-CHECKSUM"
```

Expected output:

```
IMAGE_NAME.iso: OK
sha256sum: WARNING: 8 lines are improperly formatted
```
-->