# Linux Kernel
## Download
```bash
# download and extract version 6.3.5
wget https://
```

## Install Dependencies

## Generate Build Config
```bash
# add three lines to the .config file

# make sure that CONFIG_DEBUG_INFO=y
# by default, it is
```
## Build the Kernel


# QEMU
> [!warning] 
> version of QEMU in ubuntu is severely out of data

## Download

## Install dependencies

## Build QEMU
```bash
./configure
make -j$(nproc)
```

# Test Program

## Setup
```bash
# create a 4MB blank filesystem

# mount the filesystem image to a directory so we can modify it

# add basic devices to the filesystem

# very important!! Unmount Filesystem after changing things!
```


# Kernel Debugging
- set up .config file
- remove all options that set reduced debug information (4 of them)
```c
CONFIG_GDB_SCRIPT = y

```

- to enable gdb use, you have to change a bunch of values in `constants.py`
- LX_VALUE -> LX_GDBPARSED