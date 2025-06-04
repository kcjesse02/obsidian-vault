you will need to download a toolchain and point your $PATH variable to it

1. Download appropiate toolchain for your system: https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads
	1. if you are on a VM or docker container, use the one corresponding to the OS of that
2. unzip file
3. go to the root of your system and add the path of the `bin` directory to the PATH variable
```bash
cd ~
export PATH=$PATH:/root/host-share/eCTF/paper/arm-gnu-toolchain-12.2.mpacbti-rel1-x86_64-arm-none-eabi/bin
```
4. look for an executable file in the `bin` directory called `arm-bin-none-gcc`. this will be the compiler you envoke to cross compile code.
