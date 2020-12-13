## lavapipe for WSL

This repo is based on [mesa3d repo 20.3](https://github.com/mesa3d/mesa/tree/20.3), the first official release includes `lavapipe`.  `lavapipe` is the Vulkan frontend in mesa that uses `llvmpipe` as software renderer.

The original `lavapipe` does not support Windows Subsystem for Linux (WSL) in Windows 10.  This repo adds workarounds to allow `lavapipe` to work in WSL and display via VcXsrv (a XDisplay server for Windows).

## Prepare WSL with Ubuntu 18.04 or 20.04
Prepare WSL for mesa 20.3.

1. Update and install needed packages.  
   ```
   sudo apt update &&
   sudo apt install build-essential git python3-pip pkg-config llvm valgrind vainfo bison flex llvm libdrm-dev libxdamage-dev libx11-dev libx11-xcb-dev libxext-dev libxcb-glx0-dev libxcb-dri2-0-dev libxcb-dri3-dev libxcb-present-dev libxcb-shm0-dev libxshmfence-dev libxxf86vm-dev x11-xserver-utils libxrandr-dev
   ```

1. Setup WSL environment vars.  
   ```
   alias python=python3 &&
   alias pip=pip3 &&
   export PATH=~/.local/bin:$PATH
   ```

1. Install python3 packages.  
   `pip install meson ninja cmake mako`

1. Get this repo.  
   `git clone https://github.com/gnsmrky/mesa-wsl`

1. 