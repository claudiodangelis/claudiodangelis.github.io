---
title: Automate PKGBUILD testing with Docker
layout: post
category: archlinux
---


I maintain the [ArchLinux](https://archlinux.org) package for [Postman](https://www.getpostman.com), the popular API Development Environment, this post discusses a script I use to make sure that each new version of the package builds and installs correctly (i.e.: no missing dependencies) before submitting it to [AUR (Archlinux User Repository)](https://aur.archlinux.org). By using **Docker**, the script runs the process into an isolated environment that mimics a fresh installation of ArchLinux, preventing you from forgetting to declare dependencies upon packages already installed on the machine.

<!--more-->

## The Code

You can browse or download the code of this script from my github account: [aur-pkgbuild-tester](https://github.com/claudiodangelis/aur-pkgbuild-tester).


## How Does It Work?

The script prepares a [Docker](https://docker.com) container where the process will run into, and accepts two arguments:

1. Path of the **directory** containing the PKGBUILD
2. Path of the **shell script** you want the container to run to check if the package is correctly installed

The [PKGBUILD](https://wiki.archlinux.org/index.php/PKGBUILD) (and its sources) and the optional tester script are mounted into the container through the Docker's _volumes_ abstraction.

How to run it:

```sh
./aur-pkgbuild-tester.sh \
    ./my-package-files \
    ./custom-test-installation.sh
```

Or

```sh
./aur-pkgbuild-tester.sh \
    ./my-package-files
```



Three stages:

1. The container with a fresh, minimal ArchLinux installation is created
2. The [PKGBUILD](https://wiki.archlinux.org/index.php/PKGBUILD) is built **inside the container**
3. If specified, the tester script is run inside the container


To spawn a minimal ArchLinux installation the script uses the [base/archlinux](https://hub.docker.com/r/base/archlinux/) (which is updated daily) and non-interactively installs the required packages to build the package (`sudo`, `binutils`, `fakeroot`).


Then, inside the container, it creates a non-root user to build the package (running `makepkg` as root [is disallowed](https://wiki.archlinux.org/index.php/makepkg)); if the build fails the script quits with a **non-zero code**, meaning that something is wrong in the PKGBUILD or the sources.

After building and installing the package, the script runs a _tester_ script, which by default is `/bin/true` but that can be overridden by passing the second argument to the script. The purpose of this tester script is making sure that the application installed works correctly.


## Conclusions


There are more consistent ways of checking for package integrity and correctness (see for example the [namcap](https://wiki.archlinux.org/index.php/namcap) tool or archlinux' [devtools](https://www.archlinux.org/packages/extra/any/devtools/) utilities), so I recommend using this script if you're bringing minor changes to a package that is already known to be working, or if you need to automate the process of updating the package: the script returns a non-zero code in case of failures (build failures, installation failures or custom tester failures), so it can be used in a larger build script or pipeline.
