---
title: The Ubuntu 20.04 (Focal Fossa) Build Environment
layout: en
---

## What This Guide Covers

This guide provides an overview of the packages, tools and settings available in the Focal Fossa environment.

## Using Ubuntu 20.04 (Focal Fossa)

To route your builds to Ubuntu 20.04 LTS, Focal, add the following to your `.travis.yml`:

```yaml
dist: focal
```
{: data-file=".travis.yml"}


## Differences from the previous release images

Travis CI Ubuntu 20.04, Focal, includes the following changes and improvements:

### Third party apt-repositories removed

While third party apt-repositories are used during the image provisioning, they are all removed from the build image. This has two benefits; a) reduced risk of unrelated interference and b) faster apt-get updates.

To specify a third party apt-repository, you can [add the source with the apt addon](/user/installing-dependencies/#adding-apt-sources) and specify the packages. For example:

```yaml
dist: focal
addons:
  apt:
    sources:
      - sourceline: 'git-core/ppa'
    packages:
    - git-ppa
```
{: data-file=".travis.yml"}

If you depend on these repositories in your build, you can use the following `source` line to get them back:

| package              | source                       |
|:---------------------|:-----------------------------|
| couchdb              | `deb https://apache.bintray.com/couchdb-deb $(lsb_release -cs) main`         |
| docker               | `deb https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable`              |
| google-chrome-stable | `deb http://dl.google.com/linux/chrome/deb/ stable main`              |
| git-ppa              | `ppa:git-core/ppa`           |
| haskell              | `ppa:hvr/ghc`                |
| mongodb              | `deb https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/4.4 multiverse`         |
{: style="width: 80%" }

### Services disabled by default

On the Ubuntu 20.04 based environment, to speed up boot time and improve performance we've disabled all services by default.
Add any services that you want to start to your `.travis.yml`:


```yaml
services:
  - mysql
  - redis
```
{: data-file=".travis.yml"}

## Environment common to all Ubuntu 20.04 images

The following versions of Docker, version control software and compilers are present on all Ubuntu 20.04 builds, along with more language specific software described in more detail in each language section.

All preinstalled software not provided by distro is installed from an official release --
either a prebuilt binary if available, or a source release built with default options.
For preinstalled language interpreters, a standard version manager like `rvm` is used if available for the language.

### Version control

| package | version  |
|:--------|:---------|
| git     | `2.45.2` |
| git-lfs | `2.9.2`  |
| hg      | `6.5.2`    |
| svn     | `1.13.0` |
{: style="width: 30%" }

### Compilers and Build toolchain

| package | version  |
|:--------|:---------|
| clang      | `16.0.0`  |
| llvm       | `18.0.0` |
| cmake      | `3.29.0` |
| gcc        | `9.4.0`  |
| ccache     | `3.7.7`  |
| shellcheck | `0.10.0`  |
| shfmt      | `3.8.0`  |
{: style="width: 30%" }

To use the IBM Advance Toolchain v14 compilers under `ppc64le` architecture in Focal LXD image, use the following paths in your `.travis.yml`:

- GCC compiler
  - Path: `/opt/at14.0/bin/gcc`
  - Command: `/opt/at14.0/bin/gcc hello_world.c -o hello_world`

- g++ compiler
  - Path: `/opt/at14.0/bin/g++`
  - Command: `/opt/at14.0/bin/g++ hello_world.cpp -o hello_world`

- Go compiler
  - Path: `/opt/at14.0/bin/gccgo`
  - Command: `/opt/at14.0/bin/gccgo hello_world.go -o hello_world`

- Python
  - First, compile Python 3.8.0 using the `python_interpreter.sh script`.
  - Python Interpreter Path: `/opt/python380-at14/python3.8`
  - Build Python Command: `sudo sh python_interpreter.sh`

To use the IBM Advance Toolchain v14 compilers under `amd64` architecture in Focal LXD image, use the following paths in your `.travis.yml`:

- GCC compiler
  - Path: `/opt/at14.0/bin/powerpc64le-linux-gnu-gcc`
  - Command: `/opt/at14.0/bin/powerpc64le-linux-gnu-gcc hello_world.c -o hello_world`

- g++ compiler
  - Path: `/opt/at14.0/bin/powerpc64le-linux-gnu-g++`
  Command: `/opt/at14.0/bin/powerpc64le-linux-gnu-g++ hello_world.cpp -o hello_world`

- Go compiler
  - Path: `/opt/at14.0/bin/powerpc64le-linux-gnu-gccgo`
  - Command: `/opt/at14.0/bin/powerpc64le-linux-gnu-gccgo hello_world.go -o hello_world`

- Python
  - First, compile Python 3.8.0 using the `python_interpreter.sh script`.
  - Python Interpreter Path: `/opt/python380-amd64/python3.8`
  - Build Python Command: `sudo sh python_interpreter.sh`

### Docker

* Docker `26.1.4` is installed.
* docker-compose `2.27.1` is also available.

## Ruby support

* Pre-installed Rubies: `2.5.9`, `2.7.6` and `3.3.0`.
* The default ruby is `3.3.0`.
* Other ruby versions can be installed during build time.

## Python support

* Supported Python version is: `3.7` or higher as `2.7` has been sunsetted.
* Python `3.7.17` will be used by default when no language version is explicitly set.
* The following Python versions are preinstalled:

| alias  | version  |
| :----- | :------- |
| `3.6`  | `3.7.17` |
| `3.8`  | `3.8.18` |
| `3.9`  | `3.9.18` |
| `3.12`  | `3.12.0` |
{: style="width: 30%" }

## JavaScript and Node.js support

* For builds specifying `language: node_js`, `nvm` is automatically updated to the latest version at build time. For other builds, the stable version at image build time has been selected, which is `0.39.7`.
* The following NodeJS versions are preinstalled: `4.9.1`, `6.17.1`, `8.17.0`, `10.24.1`, `12.22.12`, `14.21.3`, `16.15`, `16.20.2`, `18.20.3` and `20.14.0`.

## Go support

* Pre-installed Go: `1.11.1`.

* Other Go versions can be installed during build time by specifying the language versions with the `go:`-key.

## JVM (Clojure, Groovy, Java, Scala) support

* Pre-installed JVMs: `openjdk8`, `openjdk9`, `openjdk10`, and `openjdk11` on x86, default is `openjdk11`.

* Other JDKs, including Oracle's, can be acquired if available by specifying `jdk`.

* The following table summarizes the Pre-installed JVM tooling versions:

| package | version |
|:--------|:--------|
| gradle  | `8.3` |
| maven   | `3.9.4` |
| groovy  | `3.0.17` |
{: style="width: 30%" }

## Perl support

* Default version on Focal is `5.32.0`
* Supported versions `5.32` and `5.33` can be installed by using the `perl:`-key.
* `TAP::Harness` v3.42 and `cpanm` (App::cpanminus) version 1.7044 are also pre-installed.

## PHP support

* For dynamic runtime selection, `phpenv` is available.
* The following PHP versions are preinstalled:

| alias  | version  |
| :----- | :------- |
| `7.4`  |  `7.4.6` |
{: style="width: 30%" }

## Databases and services

The following services and databases are preinstalled but but do not run by default.
To use one in your build, add it to the services key in your `travis.yml` :

| service    | version        |
|:-----------|:---------------|
| mongodb    | `4.4.29`        |
| mysql      | `8.0.37`       |
| redis      | `7.2.5`        |
| postgresql | `13.15`         |
{: style="width: 30%" }

## Other Ubuntu Linux Build Environments

You can have a look at the [Ubuntu Linux overview page](/user/reference/linux/) for the different Ubuntu Linux build environments you can use.
