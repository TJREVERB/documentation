---
description: Runtime Modes govern the way pFS acts under different power levels
---

# Modes

## Description

`Modes` are the way `pFS` controls its behavior under different power levels. For example, when there is sufficient power present to power all hardware, the `Mode` is `NORMAL_MODE`, and all `submodules` behave normally. When there is not sufficient power to power all hardware, the `Mode` is `LOW_POWER_MODE` and all `submodules` do not behave normally. Instead, all `submodules` sleep until the `Mode` is set back to `NORMAL_MODE`.

Under `NORMAL_MODE`, it can be assumed that all the hardware is current powered on and operating normally. 

Under `LOW_POWER_MODE`, it cannot be assumed that all the hardware is current powered on and operating normally, therefore it must be assumed that no hardware is currently powered on.

## Core Mode Switching

Power levels are monitored through a `core` process. `core` has a `ThreadHandler` object that continuously queries `eps` for the current power level. If the power level is within the normal threshold defined in `helpers/power.py` , `core` shall set the `Mode` to `NORMAL_MODE`. An enum defined in `helpers/mode.py` is used to map `NORMAL_MODE` and `LOW_POWER_MODE` to integers.

This power monitoring thread is described [here](../core/processes.md#power-monitoring).

Learn more about enums [here](https://docs.python.org/3/library/enum.html).

{% code title="helpers/mode.py" %}
```python
from enum import Enum


class Mode(Enum):
    """
    Enumerate that represents Modes as integers
    """
    NORMAL = 0
    LOW_POWER = 1
```
{% endcode %}

{% code title="helpers/power.py" %}
```python
from enum import Enum


class Power(Enum):
    """
    Enumerate object that contains power thresholds for each power mode
    """
    STARTUP = 8.2
    NORMAL = 7.6
```
{% endcode %}

Once `core` determines that it is time to switch into `NORMAL_MODE`, it shall call `core.enter_normal_mode()`. Conversely, if core determines that it is time to switch into `LOW_POWER_MODE`, it shall call `core.enter_low_power_mode()`. `enter_normal_mode()` and `enter_low_power_mode()` iterates through `core.submodules` and calls `enter_*_mode()` for each submodule. In addition, it also switches the `core.state` variable to the `Mode` it switches into.

```python
def enter_normal_mode(self, reason: str = '') -> None:
    """
    Enter normal power mode.
    :param reason: Reason for entering normal mode.
    """
    self.logger.warning(
        f"Entering normal mode{'  Reason: ' if reason else ''}{reason if reason else ''}")
    self.state = Mode.NORMAL
    for submodule in self.submodules:
        if hasattr(self.submodules[submodule], 'enter_normal_mode'):
            self.submodules[submodule].enter_normal_mode()
```

```python
def enter_low_power_mode(self, reason: str = '') -> None:
    """
    Enter low power mode.
    :param reason: Reason for entering low power mode.
    """
    self.logger.warning(
        f"Entering low power mode{'  Reason: ' if reason else ''}{reason if reason else ''}")
    self.state = Mode.LOW_POWER
    for submodule in self.submodules:
        if hasattr(self.submodules[submodule], 'enter_low_power_mode'):
            self.submodules[submodule].enter_low_power_mode()
```

## Submodule Modes Switching

As part of the `submodule`class, each `submodule` has a `enter_normal_mode` and a `enter_low_power_mode` function. These methods `raise NotImplementedErrors` until they are override by each child class. 

This `enter_normal_mode` function is taken from `aprs`

```python
def enter_normal_mode(self):
    """
    Enters the APRS into normal mode.
    Re-opens the serial port and resumes the listening thread
    Assumes APRS is in low power mode.
    """
    
    self.serial.open()
    self.processes["listen_thread"].resume()
```

This method reopens the APRS serial port and resumes the listening thread for `aprs`.

This `enter_low_power_mode` function is taken from `aprs`:

```python
def enter_low_power_mode(self):
    """
    Enters the APRS into low power mode.
    Closes the serial port and pauses the listening thread
    Assumes APRS is in normal mode
    """
    self.processes["listen_thread"].pause()
    self.serial.close()
```

This method closes the APRS serial port, because the APRS radio is turned off and therefore the serial port cannot be accessed. It also stops the `listen` thread because there is no longer a serial port to read from. 

Regardless, `enter_normal_mode` and `enter_low_power_mode` are different for each `submodule`.

