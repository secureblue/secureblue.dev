---
title: "Contributing | secureblue"
description: "How to contribute to secureblue"
permalink: /contributing
---

# Contributing

All types of contributions are encouraged and valued. See the [Table of Contents](#table-of-contents) for different ways to help and details about how this project handles them. Please make sure to read the relevant section before making your contribution. It will make it a lot easier for us maintainers and smooth out the experience for all involved. The community looks forward to your contributions.

And if you like the project, but just don't have time to contribute, that's fine. There are other easy ways to support the project and show your appreciation, which we would also be very happy about:

- Star the project [in GitHub](https://github.com/secureblue/secureblue)
- Post about it on social media
- Refer this project in your project's README
- Mention the project at local meetups and tell your friends/colleagues

## [Table of Contents](#table-of-contents)

- [Code of Conduct](#code-of-conduct)
- [I Have a Question](#i-have-a-question)
- [I Want To Contribute](#i-want-to-contribute)
  - [Legal Notice](#legal-notice)
  - [AI Content Policy](#ai-content)
  - [Reporting Bugs](#reporting-bugs)
  - [Pull Requests](#pull-requests)
  - [How to test incoming changes](#how-to-test-incoming-changes)
- [Building with GitHub Actions (recommended)](#building-ga)
  - [Setting up your test build branch](#test-branch-ga)
  - [Preparing Image and Kernel signing key](#signing-keys-ga)
  - [Preparing GitHub Actions CI Workflow](#workflow-ga)
  - [Recommended Git Workflow for Pull Requests](#workflow-git)
  - [Building and Rebasing to your Image Repository](#rebase-ga)
- [Building Locally](#building-locally)
- [Styleguides](#styleguides)
  - [Commit Messages](#commit-messages)

## [Code of Conduct](#code-of-conduct)
{: #code-of-conduct}

This project and everyone participating in it is governed by the [Code of Conduct](/code-of-conduct).
By participating, you are expected to uphold this code. Please report unacceptable behavior
to [secureblueadmin@proton.me](mailto:secureblueadmin@proton.me).

## [I Have a Question](#i-have-a-question)
{: #i-have-a-question}

If you want to ask a question, you are welcome to do so in [our Discord server](https://discord.gg/qMTv5cKfbF).

## [I Want To Contribute](#i-want-to-contribute)
{: #i-want-to-contribute}

### [Legal Notice](#legal-notice)
{: #legal-notice}

When contributing to this project, you must agree that you have authored 100% of the content, that you have the necessary rights to the content and that the content you contribute may be provided under the project license.

### [AI Content Policy](#ai-content)
{: #ai-content}

In the interest of accuracy, quality, and license of the project, contributing using AI generated code and content of any kind is forbidden.

### [Reporting Bugs](#reporting-bugs)
{: #reporting-bugs}

#### Before Submitting a Bug Report

A good bug report should describe the issue in detail. Generally speaking:

- Make sure that you are using the latest version.
- Remember that these are unofficial builds, it's usually prudent to investigate an issue before reporting it here or in Fedora!
- Collect information about the bug:
  - `rpm-ostree status -v` usually helps
- Image and Version
- Possibly your input and the output
- Can you reliably reproduce the issue? And can you also reproduce it with older versions?

### [Pull Requests](#pull-requests)
{: #pull-requests}

#### Before Submitting a Pull Request

A good pull request should be ready for review before it is even created. For all pull requests, ensure:

- You have no unnecessary changes, including whitespace changes
- You have tested your changes
- For substantive changes, you include evidence of proper functionality in the pull request in addition to the build results.
- Your commits are [verified](https://docs.github.com/en/authentication/managing-commit-signature-verification)

### [How to test incoming changes](#how-to-test-incoming-changes)
{: #how-to-test-incoming-changes}

One of the nice things about the image model is that we can generate an entire OS image for every change we want to commit, so this makes testing way easier than in the past. You can rebase to it, see if it works, and then move back. This also means we can increase the amount of testers!

We strive towards a model where proposed changes are more thoroughly reviewed and tested by the community. So here's how to do it. If you see a pull request that is opened up on an image you're following, you can leave a review on how it's working for you.

## [Building with GitHub Actions (recommended)](#building-ga)
{: #building-ga}

Building with GitHub Actions allows you to create your own bootable images to rebase your Fedora Atomic installation to in order to test pull requests/features you are developing.

### [Setting up your test build branch](#test-branch-ga)
{: #test-branch-ga}

Start from your own fork with a branch for building your images. It is recommended to keep the branch you want to build your images from separate from branches you want to create pull requests from to keep the changes to your workflow separate from your feature changes and avoid merge conflicts. You can create a build branch called `test-build` by doing the following:

```sh
# Add upstream remote if wasn't already added
git remote add upstream https://github.com/secureblue/secureblue

# Fetch latest changes from upstream/live 
git fetch upstream live

# Create your test-build branch based on upstream/live
git switch -c test-build upstream/live
```

### [Preparing Image and Kernel signing key](#signing-keys-ga)
{: #signing-keys-ga}

Follow the instructions [here](https://blue-build.org/how-to/cosign/) to add your own keys to verify your own custom image. Then create two copies of your your public singing key in `files/system/etc/pki/containers/` called  `GITHUB_REPOSITORY_OWNER-2025.pub` and `GITHUB_REPOSITORY_OWNER.pub`.

You must also add a kernel signing key to your repository secrets for verifying kernel modules. [The kernel signing makes use of an X.509 certificate](https://www.kernel.org/doc/html/latest/admin-guide/module-signing.html), in which you can generate your own or use the test key pair provided in the repository. Note that the key pair in the repository is for test purposes only, as using it in production would allow the loading of malicious kernel modules that seem legitimate. If using the test key pair provided, first copy the private key `.github/workflows/private_key.priv.test` to a GitHub repository secret called `KERNEL_PRIVKEY` alongside your signing key you created to verify your public image. Then copy the public key `.github/workflows/pub_key.der.test` to `files/system/etc/pki/akmods/certs/`.

### [Preparing GitHub Actions CI Workflow](#workflow-ga)
{: #workflow-ga}

To prepare the CI workflow in GitHub Actions, it's recommended you go to `.github/workflows/build-all.yml` and comment out all of the image variants except the ones you use/intend to test. For the images you haven't commented out, you must make sure to change the `github.triggering_actor` to the GitHub username that will trigger the workflow.

### [Recommended Git Workflow for Pull Requests](#workflow-git)
{: #workflow-git}

This is a recommended git workflow you can adopt, assuming you've already [created a test build branch](#test-branch-ga) called `test-build` tracking the upstream secureblue repository.

Once all of your [signing keys](#signing-keys-ga) and [GitHub workflow](#workflow-ga) changes ready on `test-build`, commit your changes and create a tag called `test-build-setup` so we can use it later:

```sh
git add .
git commit -m "DO NOT MERGE: test-build setup"
git tag -f test-build-setup
git push origin test-build
```

You then may want to create a branch for your pull request/feature based on `upstream/live`, for example:

```sh
# Fetch latest changes from upstream/live 
git fetch upstream live
git switch -c new-feature upstream/live

# ... Do the changes ...

git add .
git commit -S -m "feat: ducks can now fly"
git push origin new-feature
```

Switch back to `test-build` and cherry pick or merge your changes from `new-feature`.

```sh
git switch test-build
git cherry-pick <hash> # Or simply 'git merge new-feature' for the full branch.
git push origin test-build
```

You may then go on to [build and rebase to your image](#rebase-ga) to test your pull request.

You can sync your build branch with upstream by doing the following:

```sh
# Make sure we're on test-build
git switch test-build

# Fetch latest changes from upstream/live 
git fetch upstream live

# Reset the branch to upstream/live
git reset --hard upstream/live

# Cherry pick the tag defined before
git cherry-pick test-build-setup

# ... Cherry pick or commit changes ...

# Push rewriting the history 
git push --force-with-lease origin test-build
```

### [Building and Rebasing to your Image Repository](#rebase-ga)
{: #rebase-ga}

Once everything is ready, go to **Actions** > **build** and select run workflow, making sure you select the branch you just set up.

Once it's done building, go to your VM running Fedora Atomic and rebase to your newly built image. If you're working from a secureblue image which rejects container images by default, make sure to [add your image registry to the container policy](/faq#container-policy). Your image registry is called `ghcr.io/YOURUSERNAME`.

To rebase to your image on your remote registry without a signature, your command will start with `rpm-ostree rebase ostree-unverified-registry:`, followed by the registry and package name. This can be found by checking the "packages" section in the sidebar of your fork. Take the docker pull command and copy the registry and package reference. Then, append the tag, which is in the format `br-{branchName}-{fedoraVersion}` (e.g. `br-test-build-43`) or just `latest` if it the image is built on your repository's default branch. If your image is not built on the default branch, your command should look like this:

```
rpm-ostree rebase ostree-unverified-registry:ghcr.io/YOURUSERNAME/YOURIMAGENAME:br-YOURBRANCHNAME-FEDORAVERSION
```

Likewise, on the default branch your command will look like this:

```
rpm-ostree rebase ostree-unverified-registry:ghcr.io/YOURUSERNAME/YOURIMAGENAME:latest
```

## [Building Locally](#building-locally)
{: #building-locally}

The minimum tools required are git and a working machine with Podman enabled and configured.
Building locally is much faster than building in GitHub and is a good way to move fast before pushing to a remote.

### Clone the repo

```
git clone https://github.com/secureblue/secureblue.git
```

### Build the image

First, make sure you can build an existing image:

```
podman build . -t something
```

Then confirm your image built:

```
podman image ls
```

TODO: Set up and push to your own local registry

### Make your changes

This usually involved editing the `Containerfile`. Most techniques for building containers apply here. If you're new to containers, using the term "Dockerfile" in your searches usually shows more results when you're searching for information.

Check out CoreOS's [layering examples](https://github.com/coreos/layering-examples) for more information on customizing.

## Using Blue-build locally

### Requirements

- [Blue-build CLI](https://github.com/blue-build/cli)
- Podman
- git

secureblue already includes `bluebuild`, but running locally requires customizing policy.json for your user to allow pulling a few unsigned images.

#### Policy.json configuration

On secureblue, only pre-configured signed images are allowed to be pulled. The following repos need to be configured:

- docker.io/mikefarah/yq `Unsigned`
- ghcr.io/blue-build/cli `Unsigned`
- ghcr.io/blue-build/modules `Unsigned`
- quay.io/fedora-ostree-desktops `Unsigned`

Copy `/etc/containers/policy.json` to `~/.config/containers/policy.json` and then add rules using Podman CLI:

- `podman image trust set --type accept docker.io/mikefarah/yq`
- `podman image trust set --type accept ghcr.io/blue-build`
- `podman image trust set --type accept quay.io/fedora-ostree-desktops`

### Making changes

Configuration is stored in `recipes` folder in form of YAML files. Other files to be added to the image are stored in `files`. `common` holds pluggable modules to add to your custom image. `general` and `securecore` hold configs for the desktop and server images, respectively. Modules are detailed in [BlueBuild's documentation](https://blue-build.org/learn/getting-started/).

### Building

- Open a terminal in the root of your cloned repo.
- Run `bluebuild build recipes/<your custom recipe>.yml`

### Testing

Run the image using `podman run` to get a root shell in your newly built image and verify the changes made prior.

## [Styleguides](#styleguides)
{: #styleguides}

### [Commit Messages](#commit-messages)
{: #commit-messages}

We use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) and enforce them with a bot to keep the changelogs tidy:

```

chore: add Oyster build script
docs: explain hat wobble
feat: add beta sequence
fix: remove broken confirmation message
refactor: share logic between 4d3d3d3 and flarhgunnstow
style: convert tabs to spaces
test: ensure Tayne retains clothing

```
