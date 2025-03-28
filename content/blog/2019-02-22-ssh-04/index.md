---
slug: ssh-04
title: Bugfix release for the ssh package
date: '2019-02-22'
author: Jeroen Ooms
topicid: 1577
tags:
  - ssh
  - packages
  - tech notes
params:
  doi: "10.59350/47ery-hp308"
---

The ssh package provides a native ssh client for R. You can connect to a remote server over SSH to transfer files via SCP, setup a secure tunnel, or run a command or script on the host while streaming stdout and stderr directly to the client. The [intro vignette](https://cran.r-project.org/web/packages/ssh/vignettes/intro.html) provides a brief introduction.

This week [version 0.4](https://cran.r-project.org/package=ssh) has been released, so you can install it directly from CRAN:

```r
install.packages("ssh")
```

The [NEWS](https://cran.r-project.org/web/packages/ssh/NEWS) file shows that this is mostly a bugfix release:

```
ssh 0.4:
  - Fix for security problem with latest openssh-server
  - Windows/Mac: update libssh to 0.8.6
  - Use new 'askpass' package for password and passphrase prompts
  - Use new ssh_session_is_known_server() api on libssh 0.8 and up
  - Fix bug that would write key to known_hosts for each connection
  - Add support for parsing ipv6 ip-address
```

There are no new features but upgrading is highly recommended.

## OpenSSH and libssh Updates


The most significant changes are due to library upgrades. The Windows and MacOS binary packages have been upgraded to the latest [libssh 0.8.6](https://www.libssh.org/2018/12/24/libssh-0-8-6-xmas-edition/). There have been numerous fixes as listed in the [libssh changelog](https://git.libssh.org/projects/libssh.git/tree/ChangeLog?h=libssh-0.8.6).

[![logo](https://www.libssh.org/wp-content/uploads/2015/01/libssh2.png)](https://www.libssh.org)


On the server side, a recent [security patch release](https://changelogs.ubuntu.com/changelogs/pool/main/o/openssh/openssh_7.2p2-4ubuntu2.7/changelog) of openssh (the standard ssh server) had caused a [problem](https://github.com/ropensci/ssh/issues/20) in the R client for copying files via SCP. It is pretty unusual that a server upgrade breaks the client in an established protocol like ssh, but apparently the R client was making a call that is no longer permitted which would cause an error, so this call has been removed.

## Authentication and Password Entry

This release also introduces several improvements to the authentication mechanics:

The R package now uses the same `~/.ssh/known_hosts` file as the ssh command line utility to store and check server fingerprints. This is an important part of the ssh protocol to protect against MITM attacks. The R client will now automatically add new hosts to the file, and check if a known server fingerprint matches the one from the file.

Finally we now use the [askpass](https://github.com/jeroen/askpass#readme) package to query the user for a password when needed. This may be needed in two cases: either when you want to log in with username/password authentication, or when reading a private key with a passphrase. With askpass we get secure native password entry programs for various R front-ends, including RStudio, RGui for Windows and R.app for MacOS.

For example, this is what it looks like on MacOS:

![macos](DlZtQrm.png)

And below a screenshot on Windows:

![winpass](VNmDmUO.png)


Hopefully this will help making the package more secure and user-friendly.
