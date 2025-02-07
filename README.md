# Alien::MSYS2 ![linux](https://github.com/hakonhagland/Alien-MSYS2/workflows/linux/badge.svg) ![windows-share](https://github.com/hakonhagland/Alien-MSYS2/workflows/windows-share/badge.svg)

Tools required for GNU style configure scripts on Windows

# SYNOPSIS

from Perl:

```perl
use Alien::MSYS2;
# runs uname from MSYS
my $uname = msys { `uname` };
```

From Prompt/Makefile

```
C:\> perl -MAlien::MSYS2 -e Alien::MSYS2::msys_run uname
```

# DESCRIPTION

MSYS provides minimal shell and POSIX tools on Windows to enable GNU style configure
scripts to run (the type usually generated by `autoconf`). This module aims to
provide an interface for using MSYS on Windows and act as a no-op on Unix like
operating systems which already have that capability.  If you use this module, I
recommend that you list this as a prerequisite only during MSWin32 installs.

When installing, this distribution will look for an existing `MSYS` using the following
methods in this order:

- environment variable `ALIEN_INSTALL_TYPE` or `ALIEN_MSYS_INSTALL_TYPE`

    If set to `share` a system install will not be attempted.  If set to `system`
    then a share install will not be attempted.

- environment variable `PERL_ALIEN_MSYS_BIN`

    If set, this environment variable should be set to the root of `MSYS2` installtion.
    For example, if you have `MSYS2` installed on `D:` you might use this:

    ```
    C:\> set PERL_ALIEN_MSYS_BIN=D:\msys64\usr\bin
    ```

    Keep in mind that this environment variable is consulted during both install and at run-time,
    so it is advisable to set this in the System Properties control panel.


If `MSYS2` cannot be found using any of these methods, then it will download and install
`MSYS2` in this distribution's share directory.

# FUNCTIONS

## msys

```perl
# get the uname from MSYS2
my $uname = msys { `uname` };

# run with GNU make from MSYS2 instead of
# dmake from Strawberry Perl
msys { system 'make' };
```

This function takes a single argument, a code reference, and runs it with the correctly
set environment so that calls to the system function or the qx quote like operator will
use MSYS2 instead of the default environment.

## msys\_run

```perl
# pass command through @ARGV
C:\> perl -MAlien::MSYS2 -e Alien::MSYS2::msys_run uname

# pass command through @_
C:\> perl -MAlien::MSYS2 -e "Alien::MSYS2::msys_run 'make'; Alien::MSYS2::msys_run 'make install'"
```

This function runs a command with the MSYS2 environment.  It gets the command and arguments
either as passed to it, or if none are passed the the command is expected to be in
@ARGV.

If the command fails then it will [exit](https://metacpan.org/pod/perlfunc#exit) with a non-zero error code.  This
is useful, in the second example above if either `make` or `make install` fails, then
the whole command will fail, also `make install` will not be attempted unless `make`
succeeds.

## msys\_path

This function returns the full path to the MSYS2 bin directory.

# CAVEATS

This [Alien](https://metacpan.org/pod/Alien) is big and slow to install.  I am aware this is an annoying problem.
It is also the only reliable way (that I know of) to install packages from source
that use autotools on Strawberry or Visual C++ Perl.  Here are some things that you
can do to avoid this painful cost:

- Use the system library if possible

    The [Alien::Build](https://metacpan.org/pod/Alien::Build) system is usually smart enough to find the system library
    if it is available.  [Alien::MSYS](https://metacpan.org/pod/Alien::MSYS) is usually only necessary for so called
    `share` installs.

- Pre-install MSYS2

    As mentioned above if you preinstall MSYS2 and set the `PERL_ALIEN_MSYS_BIN`
    environment variable, then you will save yourself some time if you use multiple
    installs of Perl like I do.

- Use another build system

    Some projects will provide a makefile that will work with GNU Make and `cmd.exe`
    that you can build without MSYS2.  An example of an Alien that takes advantage of
    this is [Alien::libuv](https://metacpan.org/pod/Alien::libuv).

    Some projects provide both autoconf and CMake.  Although using CMake reliably
    requires [Alien::cmake3](https://metacpan.org/pod/Alien::cmake3) for `share` installs, it is much much lighter than `Alien::MSYS2`.

    Also obviously you can open a ticket, or make a pull request with the project that you
    are alienizing to support build systems that don't suck as much as autoconf.

- Use MSYS2

    Strawberry Perl is convenient for building XS modules without any dependencies
    or just dependencies on the small number of libraries that come bundled with
    Strawberry Perl.  It is very very painful in my opinion if you depend on libraries
    that are not bundled, which is why this Alien exists.  There is an alternative though.

    MSYS2 / MinGW provides a MSWin32 Perl as part of a Linux / open source like package
    that provides probably all of the libraries that you might want to use as dependencies,
    and if it doesn't you can build much easier than with Strawberry + Alien::MSYS2.

    There are some notes here:

    [https://project-renard.github.io/doc/development/meeting-log/posts/2016/05/03/windows-build-with-msys2/](https://project-renard.github.io/doc/development/meeting-log/posts/2016/05/03/windows-build-with-msys2/)

    On using the MSYS2 / MinGW / MSWin32 Perl from the MSYS2 project.

# AUTHOR

Author: Graham Ollis <plicease@cpan.org>

Contributors:

Shawn Laffan (SLAFFAN, shawnlaffan)

bekuno

Zaki Mughal (zmughal)

# COPYRIGHT AND LICENSE

This software is copyright (c) 2013-2022 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
