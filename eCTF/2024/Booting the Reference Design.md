![[Screenshot 2024-01-18 at 7.20.24 PM.png|600]]
The Build consists of these four parts

### First
clone the code in this repo here:  https://github.com/kcjesse02/WPI-ectf-reference-design-exploration-2024/tree/main
## Build Environment
- Its Nix
- `nix-shell` in the directory with the `shell.nix` folder
- and poetry. There is a poetry project that comes with the build tools
- do a `poetry install`
- run the environment (poetry run) before every command

## Build Process
- each board's firmware is built separately
- There is code for the AP and the comp separately
- make sure to go into the `host-tools` folder to access the commands (or just include `./host-tools` at the beginning of the thing)

#### 1. Build Deployment:
```shell
poetry run ectf_build_depl --help
usage: eCTF Build Deployment Tool [-h] -d DESIGN

Build a deployment using Nix

options:
  -h, --help            show this help message and exit
  -d DESIGN, --design DESIGN
                        Path to the root directory of the included design
```
building the reference design
```shell
poetry run ectf_build_depl -d ../2024-ectf-insecure-example-wpi-release/
```
- This will generate a global secrets file which contains global secrets for the ap and components


#### 2. Build AP:
uses the `ectf_build_ap` command from the host tools
```shell
poetry run ectf_build_ap --help
usage: eCTF Build Application Processor Tool [-h] -d DESIGN -on OUTPUT_NAME [-od OUTPUT_DIR] -p P -b BOOT_MESSAGE

Build an Application Processor using Nix

options:
  -h, --help            show this help message and exit
  -d DESIGN, --design DESIGN
                        Path to the root directory of the included design
  -on OUTPUT_NAME, --output-name OUTPUT_NAME
                        Output prefix of the built application processor binary Example 'ap' -> a
  -od OUTPUT_DIR, --output-dir OUTPUT_DIR
                        Output name of the directory to store the result: default: .
  -p PIN, --pin PIN     PIN for built application processor
  -t TOKEN, --token TOKEN
                        Token for built application processor
  -c COMPONENT_CNT, --component-cnt COMPONENT_CNT
                        Number of components to provision Application Processor for
  -ids COMPONENT_IDS, --component-ids COMPONENT_IDS
                        Component IDs to provision the Application Processor for
  -b BOOT_MESSAGE, --boot-message BOOT_MESSAGE
                        Application Processor boot message
```
building the reference AP
```shell
poetry run ectf_build_ap -d . -on ap --p 123456 -c 2 -ids "0x11111124, 0x11111125" -b "Test boot message" -t 0123456789abcdef -od build
```

provisioned for one componet
```shell
poetry run ectf_build_ap -d . -on ap --p 123456 -c 1 -ids "0x11111124" -b "Test boot message" -t 0123456789abcdef -od build
```


**elements**
1. destination `-d` the root directory of the project
2. output directory `-op` where the built code will be placed after build completion. In this case it is in a build folder
3. pin `-p` hardcoded pin for application processor
4. token `-t` the hardcoded replacement token

#### 3. Build Component:
uses the `ectf_build_comp` command from host tools
```shell
poetry run ectf_build_comp --help
usage: eCTF Build Application Processor Tool [-h] -d DESIGN -on OUTPUT_NAME [-od OUTPUT_DIR] -id COMPONENT_ID -b BOOT_MESSAGE -al
                                             ATTESTATION_LOCATION -ad ATTESTATION_DATE -ac ATTESTATION_CUSTOMER

Build an Application Processor using Nix

options:
  -h, --help            show this help message and exit
  -d DESIGN, --design DESIGN
                        Path to the root directory of the included design
  -on OUTPUT_NAME, --output-name OUTPUT_NAME
                        Output prefix of the built application processor binary Example 'ap' -> ap.bin, ap.elf, ap.img
  -od OUTPUT_DIR, --output-dir OUTPUT_DIR
                        Output name of the directory to store the result: default: .
  -id COMPONENT_ID, --component-id COMPONENT_ID
                        Component ID for the provisioned component
  -b BOOT_MESSAGE, --boot-message BOOT_MESSAGE
                        Component boot message
  -al ATTESTATION_LOCATION, --attestation-location ATTESTATION_LOCATION
                        Attestation data location field
  -ad ATTESTATION_DATE, --attestation-date ATTESTATION_DATE
                        Attestation data date field
  -ac ATTESTATION_CUSTOMER, --attestation-customer ATTESTATION_CUSTOMER
                        Attestation data customer field
```

building the reference Component 1
```shell
poetry run ectf_build_comp -d . -on comp1 -od build -id 0x11111124 -b "Component boot" -al "Minnesota" -ad "19/01/24" -ac "Niko"
```

building the reference Component 2
```shell
poetry run ectf_build_comp -d . -on comp2 -od build -id 0x11111125 -b "Component boot" -al "Minnesota" -ad "19/01/24" -ac "Niko"
```


## Getting code on the board
### Or updating
- make sure only one device in the hub is powered
- the port might need to be switched out
	- this can be checked on mac (and linux) with `ls /dev/tty.*`
	- in windows, look in the device manager (COM)
- put the boards into update mode. Hold the `BOOTLOADER BIN` button down and power cycle the board. A blue led should flash!!

Update AP
```shell
poetry run ectf_update --infile ./build/ap.img --port PORT_NUM_AP
```
Update Component 1
```shell
poetry run ectf_update --infile ./build/comp1.img --port PORT_NUM_C1
```
Update Component 2
```shell
poetry run ectf_update --infile ./build/comp2.img --port PORT_NUM_C2
```
## List Components
```shell
poetry run ectf_list -a PORT_NUM_AP
```
## Boot
```
poetry run ectf_boot -a PORT_NUM_AP
```
`-a` is the serial port of the ap


# Open OCD

init openOCD for ap
```shell
openocd -f ap.cfg -t target/max78000.cfg -c "init"
```

ap.cfg

```cfg
#ARM CMSIS-DAP Adapter 
adapter driver cmsis-dap

adapter serial [Serial Number]
```

flash with openOCD
```shell
openocd -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "init; reset halt; max32xxx mass_erase 0; program {program.elf} verify exit"
```
- replace `program.elf` with the name of the program u want to flash!
- used `application_processor/build/max78000.elf`
so
```shell
openocd -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "init; reset halt; max32xxx mass_erase 0; program application_processor/build/max78000.elf verify exit"
```

Initializing a connection
```shell
openocd -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "init"
```

Starting GDB
```shell
arm-none-eabi-gdb ./application_processor/build/max78000.elf
```

and connecting to board with GDB
```shell
(gdb) tar ext :3333
```

## Debugger Flag
```shell
poetry run ectf_update --infile ./debugger_flag/gdb_challenge.img --port /dev/tty.usbmodem142202
```

run the file
```shell
arm-none-eabi-gdb ./gdb_challenge_mine.bin
```

set uup a serial screen in another port?
```shell
screen /dev/tty.usbmodem143302 115200
```

important values
value1 = 0x10011f00
value2 = 0x2001ffe0
value 3 = 0xe2517422

```
set $r0=0xb500b40f

set $r2=0x2001ffe0

set *0x2001ffe0=0xe2517422
```