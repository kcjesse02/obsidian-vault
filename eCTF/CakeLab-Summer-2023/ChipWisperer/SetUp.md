## Components
1. CW-Lite capture board
2. target baseboard
3. target board (can be any microcontroller board that fits on the baseboard. We will be using the sample XMEGA target)
4. SMA cable
5. 20-pin cable
6. microusb to Usb(or microusb to any type of port your computer can connect to) to connect to your computer
7. chip-whisperer virtual machine(download here: https://chipwhisperer.readthedocs.io/en/latest/virtual-box-inst.html)
![[Screenshot 2023-06-26 at 10.34.01 AM.png]]

## Setup Hardware Components
1. place the target board on the baseboard (carefully)
2. connect the SMA cable to the port labeled vout on the baseboard. then connect the other end to the chipwhisperer. 
	1. There are two sma ports on the chipwhisperer. One for glitching and one for capture, labeled glitch and measure respectively. Make sure to connect to the one corresponding to the operation you want to do!
3. connect the 10 pin cable to the baseboard and the chipwhisperer
4. connect the Chipwhisperer to your computer using the microusb cable
![[Screenshot 2023-06-27 at 2.06.09 PM.png]]

**note:** for glitching and measuring with the eCTF board (or anything other than the sample target boards), we will connect it directly to the chipwhisperer. It will not fit on the baseboard.


## Development Environment setup (with VirtualBox)
1. Download the VM (https://chipwhisperer.readthedocs.io/en/latest/virtual-box-inst.html)
2. Start VirtualBox with root permissions with `sudo virtualbox`
3. Make sure the ChipWhisperer is plugged into your computer now!
4. import the .iso file as a new virtual machine. Make sure to add the ChipWhisperer if it is not already in the list of available usb devices.
5. start the VM and log in. This will start the Jupyter server.
6. go to the USB tab and enable the ChipWhisperer connection if it is not already enabled
	1. If you can't find the ChipWhisperer, restart the VM
7. go to `localhost:8888` on your local machine to access the Jupyter server.
8.  go into the `jupyter` folder and start with the `Connecting to Hardware` lab to test if your setup is working!

## Common Errors
`USB_BUSY` - unplug it and replug it back in
`Can't find chipwhisperer` - make sure the virtual machine recognizes the chipwhisperer. 

- For VMWare, you should just be able to follow the given instructions (That worked for Iv). You don't need to specify sudo permissions, the USB devices should just connect.

