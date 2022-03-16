## Valkan (lavapipe) for WSL

This repo (https://github.com/gnsmrky/wsl-vulkan-mesa) is based on [mesa 20.3 repo](https://github.com/mesa3d/mesa/tree/20.3), the 1st official release includes `lavapipe`. `lavapipe` is the Vulkan frontend in mesa that uses `llvmpipe` as software renderer.

The original `lavapipe` does not support Windows Subsystem for Linux (WSL) in Windows 10. This repo adds workarounds to allow `lavapipe` to work in WSL and display via VcXsrv.

## Prerequisite

1. WSL Ubuntu 18.04.5 or 20.04.1
1. VcXsrv for Windows - Windows X Sever
   This repo uses VcXsrv as Windows X Server on Windows 10. Haven't tested with other X Server alternatives. Pls leave your feedbacks in [`issues`](https://github.com/gnsmrky/wsl-vulkan-mesa/issues) if you reccommend others.
      - Download and install `VcXsrv` from https://sourceforge.net/projects/vcxsrv/.

## Pre-built releases

1. Install required packages
      ```
      sudo apt update
      sudo apt install llvm libxcb-randr0 libxcb-shm0 libxcb-xfixes0
      ```
2. Download tarball binaries from [Releases](https://github.com/gnsmrky/wsl-vulkan-mesa/releases).
3. Extract to `~/mesa-local` folder
      - Ubuntu 18.04
           ```
           mkdir ~/mesa-local
           tar xzvf wsl-ubuntu1804-vulkan-mesa20.3-20201220.tar.gz -C ~/mesa-local
           ```
      - Ubuntu 20.04
           ```
           mkdir ~/mesa-local
           tar xzvf wsl-ubuntu2004-vulkan-mesa20.3-20201220.tar.gz -C ~/mesa-local
           ```
4. Follow [Instsall mesa and run vulkan tools](#install-mesa-and-run-vulkan-tools) to install and run vulkan

## Build mesa for WSL Ubuntu 18.04 or 20.04

Open WSL with Ubuntu 18.04 or 20.04.

1. Update and install needed packages for development.

      ```
      sudo apt update

      sudo apt install build-essential git python3-pip pkg-config llvm \
      valgrind vainfo bison flex llvm libdrm-dev libxdamage-dev libx11-dev \
      libx11-xcb-dev libxext-dev libxcb-glx0-dev libxcb-dri2-0-dev \
      libxcb-dri3-dev libxcb-present-dev libxcb-shm0-dev libxshmfence-dev \
      libxxf86vm-dev x11-xserver-utils libxrandr-dev
      ```

1. Setup python

      - Environment vars

           ```
           alias python=python3 &&
           alias pip=pip3 &&
           export PATH=~/.local/bin:$PATH
           ```

      - Install python3 packages.
        `pip install meson ninja cmake mako`

1. Get this repo and prepare for build env

      - Get this repo
        `git clone https://github.com/gnsmrky/wsl-vulkan-mesa`
      - Change directory to the repo and create & change to `build` folder.
           ```
           cd wsl-vulkan-mesa
           mkdir build
           cd build
           ```

1. In `build` folder, config & build mesa.

      - This sets the target folder at `/usr`.
           ```
           meson -Dprefix=/usr -Ddri-drivers= -Dglx=dri -Dllvm=enabled \
           -Ddri-drivers-path=/usr/local/lib/x86_64-linux-gnu/dri \
           -Dgallium-drivers="swrast" -Dplatforms=x11 -Dosmesa=gallium \
           -Dvulkan-drivers=swrast ..
           ```
      - Build mesa
        `ninja`

      - Install mesa to target folder `~/mesa-local`.
        `DESTDIR=~/mesa-local ninja install`

## Install mesa and run vulkan tools.

1. Config `VcXsrv` for vulkan (also applies to OpenGL).

      - Launch `VcXsrv` on Windows 10. Upon launching, set the following:
           - Select `Multiple window`.
           - Set `Display number` to `1`.
           - Select `Start no client` (should be default setting).
           - Disable `Native opengl`.
      - In WSL, set `DISPLAY` envar to `1`.
        `export DISPLAY=:1`

1. In WSL, copy mesa build files from `~/mesa-local` to `/usr` folder.
   Warning: This will replace mesa library files in WSL (mesa will be udpated from v20.0.8 to v20.3.0)!
   `sudo cp -R ~/mesa-local/usr/* /usr`

      - Check the mesa versio after copying files.
        `glxinfo -B | grep -i 'version string' | grep Mesa`

1. Validate and run Vulkan tools.

      - On WSL Ubuntu 18.04, install `vulkan-utils` and run `vulkaninfo`

           ```
           sudo apt install vulkan-utils
           vulkaninfo | grep -i deviceName
           ```

           Returns the following:

           ```
           WARNING: lavapipe is not a conformant vulkan implementation, testing use only.
           deviceName     = llvmpipe (LLVM 6.0.0, 256 bits)
           ```

           - Run `vulkan-smoketest`.
             ![alt text](./wsl-vulkan/vulkan-smoketest_ubuntu1804.png)

      - On WSL Ubuntu 20.04, install `vulkan-tools` and run `vkcube`.

           ```
           sudo apt install vulkan-tools
           vulkaninfo | grep -i deviceName
           ```

           Returns the following:

           ```
           error: XDG_RUNTIME_DIR not set in the environment.
           WARNING: lavapipe is not a conformant vulkan implementation, testing use only.
           deviceName     = llvmpipe (LLVM 10.0.0, 256 bits)
           ```

           - Run `vkcube`
             ![alt text](./wsl-vulkan/vkcube_ubuntu2004.png)

## References

1. `lavapipe` FAQ: [lavapipe: a _software_ swrast vulkan layer FAQ
   August 20, 2020](https://airlied.blogspot.com/2020/08/vallium-software-swrast-vulkan-layer-faq.html)
