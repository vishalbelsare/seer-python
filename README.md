# Seer Python Client
[![Build Status](https://travis-ci.org/cshenton/seer-python.svg?branch=master)](https://travis-ci.org/cshenton/seer-python)
[![Python Version](https://img.shields.io/pypi/pyversions/seer.svg)](https://pypi.org/project/seer/)
[![Coverage Status](https://coveralls.io/repos/github/cshenton/seer-python/badge.svg?branch=master)](https://coveralls.io/github/cshenton/seer-python?branch=master)

The python client for the seer forecasting server.


## Installation

The seer python client is available on PyPi, so just:

```bash
pip install seer

```
to get the latest release.


## Usage

Since this is a client library, you'll need a server to communicate with. To get
up and running just:
```bash
docker run -d -p 8080:8080 cshenton/seer
```
Check out the project repo over [here](https://github.com/cshenton/seer)

Then, to interact over localhost
```python
import datetime
import seer


HOST = "localhost:8080"

def graph(times, values, forecast):
    pass

def main():
    name = "myStream"
    period = 3600
    times = [...]
    values = [...]
    forecast_length = 100

    # Connect to the server and create a stream
    client = seer.Client(HOST)
    client.create_stream(name, period)

    # Feed in most of the data, generate a forecast
    client.update_stream(name, times, values)
    f = client.get_forecast(name, forecast_length)

    # Graph the forecast against the true result
    graph(times, values, f)

if __name__ == '__main__':
    main()
```

## (For Contributors) Generating gRPC stubs

Assuming this repo is stored in `cshenton/seer-python`, next to `cshenton/seer`,
we generate client stubs with:

```bash
# Generates seer_pb2.py and seer_pb2_grpc.py
python -m grpc_tools.protoc -I ../seer/seer --python_out=./seer --grpc_python_out=./seer ../seer/seer/seer.proto
```

Then, because the grpc devs don't care about python 3, we need to fix the relative imports.
```python
# in seer_pb2_grpc.py
# From this
import seer_pb2 as seer__pb2

# To this
from . import seer_pb2 as seer__pb2
```

I also manually remove `SeerServicer` and `add_SeerServicer_to_server`. protoc
doesn't provide an option to just generate client stubs, and I want test
coverage numbers to be correct.