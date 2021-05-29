---
title: "Build"
---

## Git

When downloading the source code using Git, it is important to download also
the required submodules:

```sh
git clone --recursive https://github.com/lite-xl/lite-xl.git
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

TODO
