Leveraging that datasheet enabled you to provide the correct pins and values to properly communicate with the device over UART. Because of this we were able to communicate with the device console and initiate a filesystem dump.

To begin analysis, we loaded the firmware in an analysis tool. The kernel looks to be encrypted, but we found a second-stage bootloader that loads it. The decryption must be happening in this bootloader. There also appears to be a second UART, but we don't see any data coming from it.

Can you find the secret key it uses to decrypt the kernel?

Commands for starting the docker containers and Qemu



```bash
# first container view
sudo docker run -it --rm --device=/dev/net/tun:/dev/net/tun --cap-add NET_ADMIN -v $(pwd)/myfiles:/myfiles cbc_qemu_aarch64:latest

# second container view (for netcat listeners)
sudo docker ps | grep cbc_qemu_aarch64:latest
sudo docker exec -it <CONTAINER ID> /bin/bash

# two nc listners, 1) for the TX line 2) for the RX line
nc -l 10000
nc -l 10001

# set up qemu
export UBOOT=/myfiles/u-boot.bin
export DTB=/myfiles/device_tree.dtb

qemu-system-aarch64 -M virt,secure=on -cpu cortex-a53 -bios /myfiles/u-boot.bin -dtb /myfiles/device_tree.dtb -display none -chardev socket,host=localhost,port=10000,id=uart0 -chardev socket,host=localhost,port=10001,id=uart1 -serial chardev:uart0 -serial chardev:uart1
```

all of the commands in the nc listener:
```bash
help
?         - alias for 'help'
aes       - AES 128/192/256 CBC encryption
base      - print or set address offset
bdinfo    - print Board Info structure
blkcache  - block cache diagnostics and control
boot      - boot default, i.e., run 'bootcmd'
bootd     - boot default, i.e., run 'bootcmd'
bootefi   - Boots an EFI payload from memory
bootelf   - Boot from an ELF image in memory
booti     - boot Linux kernel 'Image' format from memory
bootm     - boot application image from memory
bootp     - boot image via network using BOOTP/TFTP protocol
bootvx    - Boot vxWorks from an ELF image
chpart    - change active partition of a MTD device
cmp       - memory compare
coninfo   - print console devices and information
cp        - memory copy
crc32     - checksum calculation
date      - get/set/reset date & time
dfu       - Device Firmware Upgrade
dhcp      - boot image via network using DHCP/TFTP protocol
dm        - Driver model low level access
echo      - echo args to console
editenv   - edit environment variable
env       - environment handling commands
erase     - erase FLASH memory
exit      - exit script
ext2load  - load binary file from a Ext2 filesystem
ext2ls    - list files in a directory (default /)
ext4load  - load binary file from a Ext4 filesystem
ext4ls    - list files in a directory (default /)
ext4size  - determine a file's size
false     - do nothing, unsuccessfully
fatinfo   - print information about filesystem
fatload   - load binary file from a dos filesystem
fatls     - list files in a directory (default /)
fatmkdir  - create a directory
fatrm     - delete a file
fatsize   - determine a file's size
fatwrite  - write file into a dos filesystem
fdt       - flattened device tree utility commands
flinfo    - print FLASH memory information
fstype    - Look up a filesystem type
fstypes   - List supported filesystem types
go        - start application at address 'addr'
gzwrite   - unzip and write memory to block device
help      - print command description/usage
iminfo    - print header information for application image
imxtract  - extract a part of a multi-image
itest     - return true/false on integer compare
ln        - Create a symbolic link
load      - load binary file from a filesystem
loadb     - load binary file over serial line (kermit mode)
loads     - load S-Record file over serial line
loadx     - load binary file over serial line (xmodem mode)
loady     - load binary file over serial line (ymodem mode)
loop      - infinite loop on address range
ls        - list files in a directory (default /)
lzmadec   - lzma uncompress a memory region
md        - memory display
mii       - MII utility commands
mm        - memory modify (auto-incrementing address)
mtd       - MTD utils
mtdparts  - define flash/nand partitions
mw        - memory write (fill)
net       - NET sub-system
nfs       - boot image via network using NFS protocol
nm        - memory modify (constant address)
nvme      - NVM Express sub-system
panic     - Panic with optional message
part      - disk partition related commands
pci       - list and access PCI Configuration Space
ping      - send ICMP ECHO_REQUEST to network host
poweroff  - Perform POWEROFF of the device
printenv  - print environment variables
protect   - enable or disable FLASH write protection
pxe       - commands to get and boot from pxe files
qfw       - QEMU firmware interface
random    - fill memory with random pattern
reset     - Perform RESET of the CPU
run       - run commands in an environment variable
save      - save file to a filesystem
saveenv   - save environment variables to persistent storage
scsi      - SCSI sub-system
scsiboot  - boot from SCSI device
setenv    - set environment variables
setexpr   - set environment variable as the result of eval expression
showvar   - print local hushshell variables
size      - determine a files size
sleep     - delay execution for some time
source    - run script from memory
sysboot   - command to get and boot from syslinux files
test      - minimal test like /bin/sh
tftpboot  - boot image via network using TFTP protocol
tpm       - Issue a TPMv1.x command
tpm2      - Issue a TPMv2.x command
true      - do nothing, successfully
unlz4     - lz4 uncompress a memory region
unzip     - unzip a memory region
usb       - USB sub-system
usbboot   - boot from USB device
version   - print monitor, compiler and linker version
virtio    - virtio block devices sub-system
```

all of the environment variables
```
printenv
arch=arm
baudrate=115200
board=qemu-arm
board_name=qemu-arm
boot_a_script=load ${devtype} ${devnum}:${distro_bootpart} ${scriptaddr} ${prefix}${script}; source ${scriptaddr}
boot_efi_binary=load ${devtype} ${devnum}:${distro_bootpart} ${kernel_addr_r} efi/boot/bootaa64.efi; if fdt addr ${fdt_addr_r}; then bootefi ${kernel_addr_r} ${fdt_addr_r};else bootefi ${kernel_addr_r} ${fdtcontroladdr};fi
boot_efi_bootmgr=if fdt addr ${fdt_addr_r}; then bootefi bootmgr ${fdt_addr_r};else bootefi bootmgr;fi
boot_extlinux=sysboot ${devtype} ${devnum}:${distro_bootpart} any ${scriptaddr} ${prefix}${boot_syslinux_conf}
boot_net_usb_start=usb start
boot_pci_enum=pci enum
boot_prefixes=/ /boot/
boot_script_dhcp=boot.scr.uimg
boot_scripts=boot.scr.uimg boot.scr
boot_syslinux_conf=extlinux/extlinux.conf
boot_targets=usb0 scsi0 virtio0 dhcp
bootcmd=run distro_bootcmd
bootcmd_dhcp=devtype=dhcp; run boot_net_usb_start; run boot_pci_enum; if dhcp ${scriptaddr} ${boot_script_dhcp}; then source ${scriptaddr}; fi;setenv efi_fdtfile ${fdtfile}; setenv efi_old_vci ${bootp_vci};setenv efi_old_arch ${bootp_arch};setenv bootp_vci PXEClient:Arch:00011:UNDI:003000;setenv bootp_arch 0xb;if dhcp ${kernel_addr_r}; then tftpboot ${fdt_addr_r} dtb/${efi_fdtfile};if fdt addr ${fdt_addr_r}; then bootefi ${kernel_addr_r} ${fdt_addr_r}; else bootefi ${kernel_addr_r} ${fdtcontroladdr};fi;fi;setenv bootp_vci ${efi_old_vci};setenv bootp_arch ${efi_old_arch};setenv efi_fdtfile;setenv efi_old_arch;setenv efi_old_vci;
bootcmd_scsi0=devnum=0; run scsi_boot
bootcmd_usb0=devnum=0; run usb_boot
bootcmd_virtio0=devnum=0; run virtio_boot
bootdelay=2
cpu=armv8
distro_bootcmd=scsi_need_init=; setenv nvme_need_init; virtio_need_init=; for target in ${boot_targets}; do run bootcmd_${target}; done
efi_dtb_prefixes=/ /dtb/ /dtb/current/
fdt_addr=0x40000000
fdt_high=0xffffffff
fdtcontroladdr=46ef0290
initrd_high=0xffffffff
ivaddr=467a0010
kernel_addr_r=0x40400000
keyaddr=467a0000
load_efi_dtb=load ${devtype} ${devnum}:${distro_bootpart} ${fdt_addr_r} ${prefix}${efi_fdtfile}
loadaddr=0x40200000
nvme_boot=run boot_pci_enum; run nvme_init; if nvme dev ${devnum}; then devtype=nvme; run scan_dev_for_boot_part; fi
nvme_init=if ${nvme_need_init}; then setenv nvme_need_init false; nvme scan; fi
pxefile_addr_r=0x40300000
ramdisk_addr_r=0x44000000
scan_dev_for_boot=echo Scanning ${devtype} ${devnum}:${distro_bootpart}...; for prefix in ${boot_prefixes}; do run scan_dev_for_extlinux; run scan_dev_for_scripts; done;run scan_dev_for_efi;
scan_dev_for_boot_part=part list ${devtype} ${devnum} -bootable devplist; env exists devplist || setenv devplist 1; for distro_bootpart in ${devplist}; do if fstype ${devtype} ${devnum}:${distro_bootpart} bootfstype; then run scan_dev_for_boot; fi; done; setenv devplist
scan_dev_for_efi=setenv efi_fdtfile ${fdtfile}; for prefix in ${efi_dtb_prefixes}; do if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefix}${efi_fdtfile}; then run load_efi_dtb; fi;done;run boot_efi_bootmgr;if test -e ${devtype} ${devnum}:${distro_bootpart} efi/boot/bootaa64.efi; then echo Found EFI removable media binary efi/boot/bootaa64.efi; run boot_efi_binary; echo EFI LOAD FAILED: continuing...; fi; setenv efi_fdtfile
scan_dev_for_extlinux=if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefix}${boot_syslinux_conf}; then echo Found ${prefix}${boot_syslinux_conf}; run boot_extlinux; echo SCRIPT FAILED: continuing...; fi
scan_dev_for_scripts=for script in ${boot_scripts}; do if test -e ${devtype} ${devnum}:${distro_bootpart} ${prefix}${script}; then echo Found U-Boot script ${prefix}${script}; run boot_a_script; echo SCRIPT FAILED: continuing...; fi; done
scriptaddr=0x40200000
scsi_boot=run boot_pci_enum; run scsi_init; if scsi dev ${devnum}; then devtype=scsi; run scan_dev_for_boot_part; fi
scsi_init=if ${scsi_need_init}; then scsi_need_init=false; scsi scan; fi
stderr=pl011@9000000
stdin=pl011@9000000
stdout=pl011@9000000
usb_boot=usb start; if usb dev ${devnum}; then devtype=usb; run scan_dev_for_boot_part; fi
vendor=emulation
virtio_boot=run boot_pci_enum; run virtio_init; if virtio dev ${devnum}; then devtype=virtio; run scan_dev_for_boot_part; fi
virtio_init=if ${virtio_need_init}; then virtio_need_init=false; virtio scan; fi
```

notice, keyaddr var:
`keyaddr=467a0000`

we will use the memory display (md) command to display memory by byte at that address
`md.b 467a0000`

and that just spits out the key!