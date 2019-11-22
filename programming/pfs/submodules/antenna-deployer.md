---
description: The submodule controlling the deployment of the ISIS Antenna
---

# Antenna Deployer

## Description

`submodules/antenna_deployer/__init__.py` contains the code that interfaces with the ISIS Antenna. All that is known about the actual interfacing with the hardware is that there is a cythonized file which is imported by `antenna_deployer`. 

Learn more about cython [here](https://cython.org/).

## Code

The cythonized file is [here](https://github.com/TJREVERB/pfs/blob/master/submodules/antenna_deploy/isisants.c)

Below is `submodules/antenna_deployer/__init__.py`

{% code title="submodule/antenna\_deployer/\_\_init\_\_.py" %}
```python
from . import isisants
from helpers import log


class AntennaDeployer:

    def __init__(self, config: dict):
        self.config = config
        self.modules = dict()

    @property
    def has_modules(self):
        return len(self.modules) > 0

    def set_modules(self, dependencies: dict):
        self.modules = dependencies

    def start(self):
        # Initialize connection with device
        isisants.py_k_ants_init(b"/dev/i2c-1", 0x31, 0x32, 4, 10)

        # Arms device
        isisants.py_k_ants_arm()

        # Deploy
        isisants.py_k_ants_deploy(self.config['antenna']['ANT_1'], False, 5)
        isisants.py_k_ants_deploy(self.config['antenna']['ANT_2'], False, 5)
        isisants.py_k_ants_deploy(self.config['antenna']['ANT_3'], False, 5)
        isisants.py_k_ants_deploy(self.config['antenna']['ANT_4'], False, 5)
        if self.has_modules:
            self.modules["telemetry"].enqueue(log.Log(sys_name="antenna_deployer", lvl='INFO', msg="antenna deployed"))
        else:
            raise RuntimeError("Modules not set")
```
{% endcode %}

