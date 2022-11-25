# Shells

- [Tested shells and versions](#tested-shells-and-versions)
  - [Packages](#packages)
    - [Alpine / BusyBox / OpenWrt (LEDE)](#alpine--busybox--openwrt-lede)
    - [AlmaLinux / Rocky Linux / CentOS / Fedora](#almalinux--rocky-linux--centos--fedora)
    - [Debian / Ubuntu](#debian--ubuntu)
    - [FreeBSD](#freebsd)
    - [macOS](#macos)
    - [Windows](#windows)
  - [Manual test](#manual-test)
    - [Solaris](#solaris)
    - [OpenBSD / NetBSD](#openbsd--netbsd)
    - [AIX](#aix)
  - [Experimental](#experimental)
    - [Schily Bourne Shell](#schily-bourne-shell)
    - [GWSH shell](#gwsh-shell)
- [Confirmation for bug](#confirmation-for-bug)
- [Built-in commands](#built-in-commands)

## Tested shells and versions

- The shell included with the supported OS (the platform in bold) is the main supported shell.
- Support for very old shells may be discontinued without notice.
- The version with a dash does not work.
- The version in italic may work, but some features are not working or unstable due to bugs in the shell.
- Bourne shell (pre-POSIX shell) is not supported.

### Packages

- These are tested by [GitHub Actions][actions], [Travis CI][travis], [Cirrus CI][cirrus] and Docker (`contrib/test_in_docker.sh`).

[actions]: https://github.com/shellspec/shellspec/actions
[travis]: https://travis-ci.org/shellspec/shellspec
[cirrus]: https://cirrus-ci.com/github/shellspec/shellspec

#### Alpine / BusyBox / OpenWrt (LEDE)

Default shell: `busybox ash`

| Platform            | bash   | busybox  | dash | loksh | mksh | oksh | yash | zsh |
| ------------------- | ------ | -------- | ---- | ----- | ---- | ---- | ---- | --- |
| **Alpine 3.15.4**   | 5.1.16 | 1.34.1   |      | 7.0   |      |      | -    |     |
| Alpine edge         |        |          |      | 7.1   |      | 7.0  | -    |     |
| **BusyBox 1.34.1**  | -      | 1.34.1   | -    | -     | -    |      | -    | -   |
| LEDE 17.01.7        |        | 1.25.1   | -    | -     |      |      | -    |     |
| OpenWrt 10.03.1     |        | _1.15.3_ |      | -     |      |      | -    | -   |
| OpenWrt 12.09       |        | _1.19.4_ |      | -     |      |      | -    | -   |
| OpenWrt 14.07       |        | 1.22.1   |      | -     |      |      | -    |     |
| OpenWrt 15.05.1     |        | 1.23.2   | -    | -     |      |      | -    |     |
| OpenWrt 18.06.9     |        | 1.28.4   | -    | -     |      |      | -    |     |
| **OpenWrt 19.07.9** |        | 1.30.1   | -    | -     |      |      | -    |     |
| **OpenWrt 21.02.3** |        | 1.33.2   | -    | -     |      |      | -    |     |

- _busybox 1.15.3, 1.19.4_: `readonly` is not working properly.
- `busybox hush` not supported.

#### AlmaLinux / Rocky Linux / CentOS / Fedora

Default shell: `bash`

| Platform            | bash   | busybox | dash | ksh | mksh | yash | zsh   |
| ------------------- | ------ | ------- | ---- | --- | ---- | ---- | ----- |
| **AlmaLinux 8.5**   | 4.4.20 | -       | -    |     |      | -    |       |
| **Rocky Linux 8.5** | 4.4.20 | -       | -    |     |      | -    |       |
| **CentOS 7.9.2009** | 4.2.46 | -       | -    |     |      | -    |       |
| CentOS 8.4.2105     | 4.4.19 | -       | -    |     |      | -    | 5.5.1 |
| **fedora 34**       | 5.0.11 |         |      |     |      |      |       |
| **fedora 34**       | 5.0.17 |         |      |     |      |      |       |

- Testing bash 3.2.25- in POSIX mode only.

#### Debian / Ubuntu

Default shell: `dash` or `bash` (until debian 5.0)

| Platform         | bash   | busybox    | dash          | ksh93              | ksh2020  | mksh | pdksh    | posh     | yash | zsh     |
| ---------------- | ------ | ---------- | ------------- | ------------------ | -------- | ---- | -------- | -------- | ---- | ------- |
| Debian 2.2r7     | 2.03   | -          | -             | -                  | -        | -    | _5.2.14_ | -        | -    | 3.1.9   |
| Debian 3.0r6     | 2.05a  | ~~0.60.2~~ | ~~ash 0.3.8~~ | -                  | -        | -    | 5.2.14   | -        | -    | 4.0.4   |
| Debian 3.1r8     | 2.05b  | ~~0.60.5~~ | 0.5.2         | _93q_              | -        | -    | 5.2.14   | 0.3.14   | -    | _4.2.5_ |
| Debian 4.0r9     | 3.1.17 | _1.1.3_    | _0.5.3_       | _93r_              | -        | R28  | 5.2.14   | 0.5.4    | -    | 4.3.2   |
| Debian 5.0.10    | 3.2.39 | _1.10.2_   | 0.5.4         | 93s+ 2008-01-31    | -        | R35  | 5.2.14   | 0.6.13   | -    | 4.3.6   |
| Debian 6.0.10    | 4.1.5  | 1.17.1     | 0.5.5.1       | 93s+ 2008-01-31    | -        | R39  | 5.2.14   | _0.8.5_  | -    | 4.3.10  |
| Debian 7.11      | 4.2.37 | 1.20.0     | 0.5.7         | 93u+ 2012-02-29    | -        | R40  | -        | _0.10.2_ | 2.30 | 4.3.17  |
| Debian 8.11      | 4.3.30 | 1.22.0     | 0.5.7         | 93u+ 2012-08-01    | -        | R50d | -        | 0.12.3   | 2.36 | 5.0.7   |
| **Debian 9.13**  | 4.4.12 | 1.22.0     | 0.5.8         | 93u+ 2012-08-01    | -        | R54  | -        | 0.12.6   | 2.43 | 5.3.1   |
| **Debian 10.12** | 5.0.3  | 1.30.1     | 0.5.10.2      | 93u+ 2012-08-01    | -        | R57  | -        | 0.13.2   | 2.48 | 5.7.1   |
| **Debian 11.3**  | 5.1.14 | 1.30.1     | 0.5.11        | 93u+ 2012-08-01    | -        | R59  | -        | 0.14.1   | 2.50 | 5.8     |
| Debian Bookworm  | 5.1.16 |            |               | 93u+m 1.0.0 beta.2 | -        |      | -        |          | 2.52 | 5.8.1   |
| Ubuntu 12.04     | 4.2.25 | 1.18.5     | 0.5.7         | 93u 2011-02-08     | -        | R40  | -        | _0.10_   | 2.29 | 4.3.17  |
| Ubuntu 14.04     | 4.3.11 | 1.21.0     | 0.5.7         | 93u+ 2012-08-01    | -        | R46  | -        | 0.12.3   | 2.35 | 5.0.2   |
| **Ubuntu 16.04** | 4.3.48 | 1.22.0     | 0.5.8         | 93u+ 2012-08-01    | -        | R52c | -        | 0.12.6   | 2.39 | 5.1.1   |
| **Ubuntu 18.04** | 4.4.20 | 1.27.2     | 0.5.8         | 93u+ 2012-08-01    | -        | R56c | -        | 0.13.1   | 2.46 | 5.4.2   |
| **Ubuntu 20.04** | 5.0.16 | 1.30.1     | 0.5.10.2      | 93u+ 2012-08-01    | 2020.0.0 | R58  | -        | 0.13.2   | 2.49 | 5.8     |

- Use [debian/eol](https://hub.docker.com/r/debian/eol) docker images for older Debian versions.
- Testing bash 2.03-3.2.39 in both POSIX and non-POSIX mode. bash 4.1.5- is non-POSIX mode only.
- _pdksh 5.2.14_ (Debian 2.2.r7): `readonly` is not working properly.
- _ksh 93q, 93r_: Sometimes abort due to unstable bugs.
- _busybox 1.1.3_: Builtin commands cannot be mocked.
- _busybox 1.10.2_: It may not work properly due to a bug.
- _posh 0.8.5_: Signal handling (`trap`) broken.
- _posh 0.10, 0.10.2_: Shell flags (e.g. `set -e`, `set -u`) not working properly.
- _zsh 4.2.5_: errexit handling broken.

#### FreeBSD

Default shell: `ash`

| Platform         | FreeBSD sh | bash   | dash     | ksh             | mksh | oksh | pdksh    | zsh   |
| ---------------- | ---------- | ------ | -------- | --------------- | ---- | ---- | -------- | ----- |
| **FreeBSD 11.4** | unknown    | 5.1.8  | 0.5.11.4 | 93u+ 2012-08-01 | R59c | 6.9  | _5.2.14_ | 5.8   |
| **FreeBSD 12.3** | unknown    | 5.1.16 | 0.5.11.5 | 93u+ 2012-08-01 | R59c | 7.0  | _5.2.14_ | 5.8.1 |
| **FreeBSD 13.0** | unknown    | 5.1.16 | 0.5.11.5 | 93u+ 2012-08-01 | R59c | 7.0  | _5.2.14_ | 5.8.1 |

- _pdksh 5.2.14_: Unstable on CI environment (Bus Error, Bad address, Memory fault, core dumped and etc).

#### macOS

Default shell: `bash`

| Platform                     | sh          | bash   | dash     | ksh             | mksh | posh       | yash | zsh   |
| ---------------------------- | ----------- | ------ | -------- | --------------- | ---- | ---------- | ---- | ----- |
| macOS 10.10                  | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.0.5 |
| macOS 10.11                  | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.0.8 |
| macOS 10.12                  | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.2   |
| macOS 10.13                  | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.3   |
| **macOS 10.14.4 (Mojave)**   | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.3   |
| **macOS 10.15.5 (Catalina)** | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.7.1 |
| **macOS 11.6.2 (Big Sur)**   | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.8   |
| **macOS 12.3.1 (Monterey)**  | bash 3.2.57 | 3.2.57 | -        | 93u+ 2012-08-01 | -    | -          | -    | 5.8   |
| **macOS 12.3.1 (brew)**      | -           | 5.1.0  | 0.5.11.2 | 2020.0.0        | R59b | ~~0.14.1~~ | 2.50 | 5.8   |

- Supports the latest three versions.
- Homebrew version of posh is broken.

#### Windows

Default shell: `bash`

| Platform                              | bash   | busybox       | dash     | mksh | posh   | zsh |
| ------------------------------------- | ------ | ------------- | -------- | ---- | ------ | --- |
| **Windows Server 2019 (Git Bash)**    | 4.4.23 | -             | unknown  | -    | -      | -   |
| **Windows Server 2019 (msys)**        | 5.1.16 | 1.31.1        | 0.5.11.5 | R59  | -      | 5.8 |
| **Windows Server 2019 (cygwin)**      | 4.4.12 | 1.23.2        | 0.5.11.5 | R56c | 0.13.2 | 5.8 |
| **Windows Server 2019 (busybox-w32)** | -      | 1.35.0 (4487) | -        | -    | -      | -   |

- busybox-w32: [https://frippery.org/busybox/](https://frippery.org/busybox/)
  - Versions that do not work properly
    - 4264, 4621

### Manual test

This is not continuous test, it may break sometimes...

- \* Reported by user

#### Solaris

| Platform       | /bin/sh               | /usr/bin/ksh        | /usr/sunos/bin/ksh  | /usr/sunos/bin/sh |
| -------------- | --------------------- | ------------------- | ------------------- | ----------------- |
| **Solaris 10** | ~~Bourne Shell~~      | _ksh88 M-11/16/88i_ | -                   | -                 |
| **Solaris 11** | ksh93 93u+ 2012-08-01 | -                   | _ksh88 M-11/16/88i_ | ~~Bourne Shell~~  |

- _ksh88 M-11/16/88i_: Builtin commands cannot be mocked.

#### OpenBSD / NetBSD

| Platform           | /bin/sh                                  | /usr/bin/ksh |
| ------------------ | ---------------------------------------- | ------------ |
| FreeBSD 13 current | FreeBSD sh                               | -            |
| **OpenBSD 6.6**    | OpenBSD ksh (POSIX mode pdksh) 5.2.14    | pdksh 5.2.14 |
| **NetBSD 9.0**     | NetBSD sh 20181212 BUILD:20200214000628Z | pdksh 5.2.14 |

#### AIX

| Platform      | Shells      |
| ------------- | ----------- |
| AIX 7.2.1.4 * | bash 4.3.30 |

### Experimental

These are tested by Docker (`contrib/test_in_docker.sh`).

#### Schily Bourne Shell

| Platform        | Schily versions | bosh / pbosh |
| --------------- | --------------- | ------------ |
| Debian bullseye | AN-2021-09-18   | 2021/07/23   |

- [Schily Bourne Shell][bosh] (`bosh`, `pbosh`)
- Packages are available on [The NetBSD package collection][pkgsrc].
- Versions before 2018/10/07 not tested (probably not working).

[bosh]: http://schilytools.sourceforge.net/bosh.html
[pkgsrc]: http://pkgsrc.se/shells/bosh

#### GWSH shell

| Platform        | gwsh              |
| --------------- | ----------------- |
| Debian bullseye | snapshot-20190627 |

- [GWSH shell](https://github.com/hvdijk/gwsh/releases)

## Confirmation for bug

`contrib/bugs.sh` detects shell bugs and problems.

Usage: `contrib/bugs.sh`

## Built-in commands

`contrib/builtins.sh` is a script for listing built-in commands.

Usage: `contrib/builtins.sh`
<span style="float: footnote;"><a href="./index.html#toc">Go to TOC</a></span>
