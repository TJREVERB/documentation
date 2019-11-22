---
description: The way core is initialized and how it initializes other submodules
---

# Startup

## Description

When the `core.start` method is called, the first action `core` will take is to attempt to call the `start` method for each `submodule` in `core.submodules`. Calling the `start` method for each `submodule` is different from creating an instance of that `submodule`. Instantiating `submodule` objects are done in the `core.__init__` constructor method.

`core.start` follows this sequence:

* for the `submodules` listed in `config['core']['modules']['A']`
  * if the `submodule` has a `start` method, call the `start` method
* verify that the system is booting up for the first time
  * if the system is booting for the first time, sleep for 30 minutes
* for the `submodules` listed in `config['core']['modules']['B']`
  * if the `submodule` has a `start` method, call the `start` method
* set `core.state` to `mode.NORMAL_MODE`
* verify that the system has sufficient power to enter `NORMAL_MODE`
  * if the system does not have sufficient power, sleep until sufficient power is reached
* for the `submodules` listed in `config['core']['modules']['C']`
  * if the `submodule` has a `start` method, call the `start` method
* for the `processes` in `core.processes`
  * start the `process`

## Startup Sequence

In `config/config_default.yml`, `submodules` are listed under `A`, `B`, and `C` headers. `A` modules are started first, followed by `B` modules, followed by `C` modules. 

* `start` the `A` modules
* sleep for 30 minute if first boot
* `start` the `B` modules
* sleep until sufficient power is reached
* `start` the `C` modules

### Checking for first boot

As per regulation, the CUBESAT must sleep for 30 minutes after it boots for the first time. This check is done using the following method:

On `*nix` systems, like the `Raspberry Pi Zero W`, there is a command called `last reboot` which stores records of all the reboots. `is_first_boot` utilizes this system command to check if it is booting up for the first time

```python
def is_first_boot() -> bool:
    """
    Returns True if it is determined that the computer is booting for the first time
    """
    cmd = subprocess.Popen(['last', 'reboot'], stdout=subprocess.PIPE, stderr=None)
    reboot_num = len([x for x in cmd.communicate()[0].splitlines() if x])-1
    return reboot_num == 1 
```

### Checking for sufficient power

This is just a `While` loop that constantly queries `eps` until it reports a sufficient power level

```python
while self.submodules['eps'].get_battery_bus_volts() < Power.STARTUP.value:
    time.sleep(1)
self.mode = Mode.NORMAL
```

