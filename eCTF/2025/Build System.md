first you will want to set up a virtual environment to install the python depenancies

```bash
# Create a virtual environment in the root of the design
#only do this the first tyoe
cd <example_root>
python3 -m venv .venv --prompt ectf-example #name of the venv will be ectf example


# Enable virtual environment
. ./.venv/bin/activate

#only need to be done after first creating the venv
# Install the host tools
python3 -m pip install ./tools/

# Install the host design elements as an editable module
python3 -m pip install -e ./design/
```

### Building the deployment
this is where the secrets will be generated
```bash
mkdir secrets
# last arguments are the channels to provision for
python3 -m ectf25_design.gen_secrets secrets/secrets.json 1 3 4
```

### Building the decoder
```bash
cd ./decoder
docker build -t decoder .
docker run --rm -v ./build_out:/out -v ./:/decoder -v ./../secrets:/secrets -e DECODER_ID=0xdeadbeef decoder
```

### Generating Subscription Updates

subscription update packets (in .json format) using the `gen_subscriptions.py` script

info:
```bash
python3 -m ectf25_design.gen_subscription -h
usage: gen_subscription.py [-h] [--force] secrets_file subscription_file device_id start end channel

positional arguments:
  secrets_file       Path to the secrets file created by ectf25_design.gen_secrets
  subscription_file  Subscription output
  device_id          Device ID of the update recipient.
  start              Subscription start timestamp
  end                Subscription end timestamp
  channel            Channel to subscribe to

options:
  -h, --help         show this help message and exit
  --force, -f        Force creation of subscription file, overwriting existing file
```

example:
```bash
python3 -m ectf25_design.gen_subscription ./secrets/secrets.json subscription.bin 0xDEADBEEF 32 128 1
```

### Flashing

