This is work in progress...

## lavapipe for WSL

This repo (https://github.com/gnsmrky/mesa-wsl) is based on [mesa3d repo 20.3](https://github.com/mesa3d/mesa/tree/20.3), the first official release includes `lavapipe`.  `lavapipe` is the Vulkan frontend in mesa that uses `llvmpipe` as software renderer.

The original `lavapipe` does not support Windows Subsystem for Linux (WSL) in Windows 10.  This repo adds workarounds to allow `lavapipe` to work in WSL and display via VcXsrv (a XDisplay server for Windows).

## Build WSL with Ubuntu 18.04 or 20.04
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
   
1. Setup python in WSL.
   - Environment vars   
     ```
     alias python=python3 &&
     alias pip=pip3 &&
     export PATH=~/.local/bin:$PATH
     ```

   - Install python3 packages.  
     `pip install meson ninja cmake mako`

1. Get this repo and prepare for build env
   - Get the repo  
     `git clone https://github.com/gnsmrky/mesa-wsl`
   - Change directory to the repo and create & change to `build` folder.
      ```
      cd mesa-wsl
      mkdir build
      cd build
      ```

1. In `build` folder, config & build mesa.
   - This sets the target folder at `~/mesa_local`.
     ```
     meson -Dprefix=~/mesa_local -Ddri-drivers= -Dglx=dri -Dllvm=enabled \
     -Ddri-driver-path=/usr/local/lib/x86_64-linux-gnu/dri \
     -Dgallium-drivers="swrast" -Dplatforms=x11 -Dosmesa=gallium \
     -Dvulkan-drivers=swrast ..
     ```
   - Build mesa  
     `ninja`

   - Install mesa to target folder `~/mesa_local`.  
     `ninja install`

1. Install mesa to WSL.
   - Change to target folder.  
     `cd ~/mesa_local`
   - Copy mesa build files to `/usr` folder.  (warning: This will replace mesa files in WSL!)  
     `sudo copy -R * /usr`

1. Config `VcXsrv` for WSL Vulkan.
   - Launch `VcXsrv` on Windows 10. Upon launching, set the following:  
     - Select `Multiple window`.
     - Set `Display number` to `1`.
     - Select `Start no client` (should be default setting).
     - Disable `Native opengl`.
   - In WSL, set `Display` envar to `1`.  
     `export Display=:1`

1. Validate and run Vulkan.
   - On Ubuntu 18.04, run `vulkan-smoketest`.  
     ```
     sudo apt install vulkan-utils
     vulkaninfo
     vulkan-smoketest
     ```
     ![alt text](./vulkan-wsl/vulkan-smoketest_ubuntu1804.png)

   - On Ubuntu 20.04, run `vkcube`.  
     ```
     sudo apt install vulkan-tools
     vulkaninfo
     vkcube
     ```
     ![alt text](./vulkan-wsl/vkcube_ubuntu2004.png)
