Python Virtual Env
```bash
virtualenv ./venv #create virtual environment
source ./venv/bin/activate #activate virtual env !!IMPORTANT!!
python3 -m pip install -e ./2023-ectf-tools #install tools into env
```
### Build Environment

environment
```bash
python3 -m ectf_tools build.env --design ~/2023-mitre-ectf --name pog-system
```
host tools
```bash
python3 -m ectf_tools build.tools --design ~/2023-mitre-ectf --name pog-system
```
deployment
```bash
python3 -m ectf_tools build.depl --design ~/2023-mitre-ectf --name pog-system --deployment pog-depl
```
car and fob
```bash
python3 -m ectf_tools build.car_fob_pair --design ~/2023-mitre-ectf --name pog-system --deployment pog-depl --car-name car --car-out ~/2023-mitre-ectf/bin --fob-name fob --fob-out ~/2023-mitre-ectf/bin --car-id 1 --pair-pin 123456 --car-unlock-secret abcdefg --car-feature1-secret a --car-feature2-secret b --car-feature3-secret c
```

just fob
```shell
python3 -m ectf_tools build.fob --design . --name pog-system --deployment pog-depl --fob-out ./bin --fob-name unpairedFob
```

### Load device

car
```bash
python3 -m ectf_tools --debug device.load_hw --dev-in ./bin --dev-name car --dev-serial /dev/car
```
fob
```bash
python3 -m ectf_tools device.load_hw --dev-in ./bin --dev-name fob --dev-serial /dev/fob
```

### Run
set-up bridge
```shell
python3 -m ectf_tools device.bridge --bridge-id 9001 --dev-serial /dev/car
```
unlock
```shell
python3 -m ectf_tools run.unlock --name pog-system --car-bridge 9001
```

## Attack Phase
### firmware
car
```shell
python3 -m ectf_tools device.load_sec_hw --dev-in ./ --dev-name car0 --dev-serial /dev/ttyACM0
```
fob
```shell
python3 -m ectf_tools device.load_sec_hw --dev-in ./ --dev-name fob0 --dev-serial /dev/ttyACM
```