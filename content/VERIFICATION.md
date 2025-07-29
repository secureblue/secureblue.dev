---
title: "Verification | secureblue"
description: "Steps to verify secureblue media"
permalink: /verification
---

# Verification

## [Table of Contents](#table-of-contents)
{: #table-of-contents}

- [Pre-install](#pre-install)
- [Terms of Use](#terms)
- [Installation](#installation)
  - [Secureblue ISO (Desktop)](#iso)
    - [ISO Verification](#verification)
  - [Ignition (Server)](#ignition)
- [Post-install](#post-install)

<hr>

## [Prerequisites](#prerequisites)
{: #prerequisites}

You should now have the ISO with its corresponding CHECKSUM file, the keyring file, and if you opted to use a torrent, the torrent file with its corresponding CHECKSUM file. Use following commands to verify the ISO (where `${IMAGE_NAME}` corresponds to the filename of the ISO you downloaded).

### Windows (#windows)
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

### macOS (#macOS)
{: #macOS}

In the terminal, install [Homebrew](https://brew.sh/) if you haven't already. Then:
```
brew install gnupg coreutils
```

To temporarily enable the `sha256sum` command, run:
```
export PATH="$(brew --prefix coreutils)/libexec/gnubin:$PATH"
```

### Linux (#Linux)
{: #Linux}

If `gpg --version` gives a `command not found` error, then you do not have GPG installed. You can get this via your distribution's package manager, or by installing [Homebrew](https://brew.sh/), then:
```
brew install gnupg
```

## For torrent users (#torrent-users)
{: #torrent-users}

Before downloading, torrent users should check their torrent is authentic.

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

Second command:
```
sha256sum -c "${IMAGE_NAME}.iso.torrent-CHECKSUM"
```

Expected output:
```
IMAGE_NAME.iso.torrent: OK
sha256sum: WARNING: 8 lines are improperly formatted
```

## For all users (#all-users)
{: #all-users}

After downloading the ISO, you should verify it.

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

Second command:
```
sha256sum -c "${IMAGE_NAME}.iso-CHECKSUM"
```

Expected output:
```
IMAGE_NAME.iso: OK
sha256sum: WARNING: 8 lines are improperly formatted
```
