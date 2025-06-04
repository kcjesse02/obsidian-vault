Nix is used to create the development and build environment for the 



Building the Example Projects
1. Create an `ExampleBuild` folder
2. Create the `shell.nix` file and paste code from the ectf page 
```js
{ pkgs ? import <nixpkgs> {}
  , fetchgit ? pkgs.fetchgit
}:

pkgs.mkShell {
  buildInputs = [
    pkgs.gnumake
    pkgs.python39
    pkgs.gcc-arm-embedded
    pkgs.poetry
    pkgs.cacert
	(pkgs.callPackage analog_openocd.nix { }) //this line references the custom version of openocd
    pkgs.minicom
    //more packages can be placed here
  ];

  msdk = builtins.fetchGit {
    url = "https://github.com/Analog-Devices-MSDK/msdk.git";
    ref = "refs/tags/v2023_06";
  };
  shellHook =
    ''
      cp -r $msdk $PWD/msdk
      chmod -R u+rwX,go+rX,go-w $PWD/msdk
      export MAXIM_PATH=$PWD/msdk
    '';
}
```

3. create `analog_openocd.nix` and paste given code
```js
{ stdenv
, lib
, pkg-config
, hidapi
, jimtcl
, libjaylink
, libusb1
, libgpiod
, gcc
, gnumake
, coreutils
, autoconf
, automake
, texinfo
, git
, libtool
, which
, libftdi1
}:

stdenv.mkDerivation {
  pname = "openocd-analog";
  version = "0.12.0";

  src = builtins.fetchGit {
    url = "https://github.com/analogdevicesinc/openocd.git";
    ref = "release";
    submodules = true;
  };

  nativeBuiltInputs = [ pkg-config ];

  buildInputs = [
    hidapi
    gcc
    gnumake
    coreutils
    pkg-config
    autoconf
    automake
    texinfo
    git
    jimtcl
    libusb1
    libjaylink
    libftdi1
    libtool
    which
  ];

  postPatch = ''
    substituteInPlace src/jtag/drivers/libjaylink/autogen.sh --replace "LIBTOOLIZE=glibtoolize" "LIBTOOLIZE=libtoolize"
  '';

  enableParallelBuilding = true;

  configurePhase = ''
    SKIP_SUBMODULE=1 ./bootstrap
    ./configure --prefix=$out --disable-werror
  '';

   meta = with lib; {
    description = "OpenOCD fork for Analog Devices microcontrollers";
    longDescription = ''
      This is a fork of OpenOCD by ADI,
      which brings support to MAXIM MCUs microcontroller.
    '';
    homepage = "https://github.com/analogdevicesinc/openocd.git";
    license = licenses.gpl2Plus;
    maintainers = with maintainers; [ eCTF ];
  };
}
```
4. run `nix-shell` to install dependancies
5. navigate to the `msdk/Examples/MAX78000/Hello_World`
6. Enter this command
```shell
make -r -j BOARD=FTHR_RevA
```


Flash Command
```shell
make flash.openocd
```
Flash and Halt (For Debugging)
```bash
openocd -s $MAXIM_PATH/Tools/OpenOCD/scripts -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "program build/max78000.elf verify; init; reset halt"
```
Launch GDB For Debugging
1. Open new terminal session
2. navigate to the `msdk/Examples/MAX78000/Hello_World`
3. enter this command:
```shell
arm-none-eabi-gdb --se=build/max78000.elf
```

MSDK Documentation: https://analog-devices-msdk.github.io/msdk/USERGUIDE/#command-line-development

Available Nix Packages Database (check system availability!): https://search.nixos.org/packages

### PUF: a key
- some aspect of the hardware as the key
- SRAM startup: random array of bits (between devices) ()
	- Even looking at the SRAM will change the PUF
- DRAM charge pump on/off: turn off the charge pump for a bit and see what the bits have degraded into.
- 

### Replacement Token


# Open OCD

flash with openOCD
```shell
openocd -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "init; reset halt; max32xxx mass_erase 0; program {program.elf} verify exit"
```
- replace `program.elf` with the name of the program u want to flash!
- used `application_processor/build/max78000.elf`

Initializing a connection
```shell
openocd -f interface/cmsis-dap.cfg -f target/max78000.cfg -c "init"
```

Starting GDB
```shell
arm-none-eabi-gdb /application_processor/build/max78000.elf
```

and connecting to board with GDB
```shell
(gdb) tar ext :3333
```