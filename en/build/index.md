---
title: "Build"
---

## Git

When downloading the source code using Git, it is important to download also
the required submodules:

```sh
git clone --recursive https://github.com/franko/lite-xl.git
```

If the `--recursive` or `--recurse-submodules` was not used, it is possible
doing it as a separate step:

```sh
git submodule update --init --recursive
```

TODO

### Linux

TODO

### macOS

TODO

### MSYS2

Open `MinGW 64-bit` or `MinGW 32-bit` from the start menu.
Install tools and libraries:

- Update the MSYS2 installation with `pacman -Syu`
- Restart the shell

Install the dependencies

TODO

### Windows

TODO

## Build

You can build Lite XL yourself using Meson.

In addition, the `build-packages.sh` script can be used to compile Lite XL and
create an OS-specific package for Linux, Windows or Mac OS.

The following libraries are required:

- freetype2
- SDL2

The following libraries are **optional**:

- libagg
- Lua 5.2

If they are not found, they will be downloaded and compiled by Meson.
Otherwise, if they are present, they will be used to compile Lite XL.

On Debian-based systems the required libraries and Meson can be installed
using the following commands:

```sh
# To install the required libraries:
sudo apt install libfreetype6-dev libsdl2-dev

# To install Meson:
sudo apt install meson
# or pip3 install --user meson
```

To build Lite XL with Meson the commands below can be used:

```sh
meson setup --buildtype=release build
meson compile -C build
meson install -C build
```

If you are using a version of Meson below 0.54
you need to use diffent commands to compile and install:

```sh
meson setup --buildtype=release build
ninja -C build
ninja -C build install
```

When performing the `meson setup` command you may enable the `-Dportable=true`
option to specify whether files should be installed as in a portable application.

If `portable` is enabled, Lite XL is built to use a `data` directory placed next
to the executable.
Otherwise, Lite XL will use unix-like directory locations.
In this case, the `data` directory will be `$prefix/share/lite-xl`
and the executable will be located in `$prefix/bin`.
`$prefix` is determined when the application starts as a directory such that
`$prefix/bin` corresponds to the location of the executable.

The `user` directory does not depend on the `portable` option and will always be
`$HOME/.config/lite-xl`.
`$HOME` is determined from the corresponding environment variable.
As a special case on Windows the variable `$USERPROFILE` will be used instead.

If you compile Lite XL yourself,
it is recommended to use the script `build-packages.sh`:

```sh
bash build-packages.sh <arch>
```

The script will run Meson and create a zip file with the application or,
for linux, a tar compressed archive.
Lite XL can be easily installed by unpacking the archive in any directory of your choice.

On Windows two packages will be created, one called "portable" using the "data"
folder next to the executable and the other one using a unix-like file layout.
Both packages works correctly. The one with unix-like file layout is meant
for people using a unix-like shell and the command line.

Please note that there aren't any hard-coded directories in the executable,
so that the package can be extracted and used in any directory.

Mac OS X is fully supported and a notarized app disk image is provided in the
[release page]. In addition the application can be compiled using
the generic instructions given above.


[release page]: https://github.com/franko/lite-xl/releases
