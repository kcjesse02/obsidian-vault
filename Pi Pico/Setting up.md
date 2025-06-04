# Setup
### The SDK
```bash
//gets the sdk
git clone https://github.com/raspberrypi/pico-sdk.git --branch master
cd pico-sdk
git submodule update --init
cd ..

//gets the examples
git clone https://github.com/raspberrypi/pico-examples.git --branch master
```

Update SDK
```bash
cd pico-sdk
git pull
git submodule update
```

You'll also need the following
```bash
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential
```

# Sample: Blinking Onboard LED
This thing came with a little example to blink the LED, which is in the examples folder we cloned before

create directory for build to go in!
```bash
cd pico-examples
mkdir build
cd build

//set path to the SDK
export PICO_SDK_PATH=../../pico-sdk

//prep cmake dir
cmake ..

//blink: the example we want to use
cd blink
make
```


