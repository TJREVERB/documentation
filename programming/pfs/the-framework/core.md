---
description: This article describes the format Core should follow
---

# Core

## Description

As previously stated, `Core` acts as a system daemon and controls `pFS` on an application level. Therefore, core has the following aspects:

* `__init__()`
  * This constructor initializes the `config` YAML dictionary
  * This constructor initializes the `logger` , `state` variables
  * This constructor initializes all the `submodules` regardless of order
  * This constructor populates the dependencies of all the `submodules` regardless of order
  * This constructor initializes the `processes` dictionary
* `start()`
  * This `start` method runs through the [Startup](../core/startup.md) process
    * Read more about Startup [here](../core/startup.md)
  * This `start` method iterates through the `processes` dictionary and starts all threads

## Specifics

The following methods are specific to just `core`:

* `populate_dependencies`
  * Since the dependency tree for `submodules` is extremely circular, it is impossible to instantiate `submodules` with references to their dependencies
  * `populate_dependencies` iterates through the `config` dictionary and reads the `depeneds_on` key for each `submodule`. It then calls the `submodule.set_modules` method which populates the `self.modules` dictionary with references to the `submodules` dependencies
* `request`
  * For any reason, a `submodule` may request a reference to another `submodule`. This method grants that request
* `get_state`
  * Returns the current [Mode](../conceptual-operations/modes.md).
* `reset_module`
  * Handles the `ModuleNotFoundError` by directly accessing a `submodules` `self.modules` dictionary and repopulating it with good dependency references.
* `get_config`
  * Returns `core.config`

## Code

{% code title="core/core.py" %}
```python
import logging
import os
import time

from functools import partial
from threading import Timer
from yaml import safe_load

from helpers.mode import Mode
from helpers.power import Power
from helpers.threadhandler import ThreadHandler
from core.processes import power_watchdog, is_first_boot

from submodules.submodule import Submodule
from submodules.antenna_deployer import AntennaDeployer
from submodules.command_ingest import CommandIngest
from submodules.eps import EPS
from submodules.radios.aprs import APRS
from submodules.radios.iridium import Iridium
from submodules.telemetry import Telemetry


class Core:

    def __init__(self):
        if os.path.exists('config/config_custom.yml'):
            with open('config/config_custom.yml') as f:
                self.config = safe_load(f)
        else:
            with open('config/config_default.yml') as f:
                self.config = safe_load(f)

        self.logger = logging.getLogger("core")
        self.state = Mode.LOW_POWER
        self.submodules = {
            "antenna_deployer": AntennaDeployer(core=self, config=self.config),
            "aprs": APRS(core=self, config=self.config),
            "command_ingest": CommandIngest(core=self, config=self.config),
            "eps": EPS(core=self, config=self.config),
            "iridium": Iridium(core=self, config=self.config),
            "telemetry": Telemetry(core=self, config=self.config),
        }
        self.populate_dependencies()
        self.processes = {
            "power_monitor": ThreadHandler(
                target=partial(power_watchdog, core=self, eps=self.submodules['eps']),
                name="power_monitor",
                parent_logger=self.logger
            ),
            "telemetry_dump": Timer(
                interval=self.config['core']['dump_interval'],
                function=partial(self.submodules["telemetry"].dump)
            ),
            "telemetry_heartbeat": Timer(
                interval=self.config['core']['heartbeat_invertal'],
                target=partial(self.submodules["telemetry"].heartbeat)
            )
        }

    def populate_dependencies(self) -> None:
        """
        Iterates through configuration data dictionary and sets each submodule's self.modules dictionary
        with a dictionary that contains references to all the other submodules listed in the first 
        submodule's depends_on key
        """
        for submodule in self.submodules:
            if hasattr(self.submodules[submodule], 'set_modules'):
                self.submodules[submodule].set_modules({
                    dependency: self.submodules[dependency]
                    for dependency in self.config[submodule]['depends_on']
                })

    def get_config(self) -> dict:
        """Returns the configuration data from config_*.yml as a list"""
        return self.config

    def get_state(self) -> Mode:
        return self.state

    def reset_module(self, submodule: Submodule, dependency: str) -> None:
        submodule.modules[dependency] = self.submodules[dependency]

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

    def request(self, module_name: str):
        """
        Returns a reference to a specified module if specified module is present
        @param module_name: name of module requested
        """
        return self.submodules[module_name] if module_name in self.submodules.keys() else False

    def __str__(self):
        return "Core: core"

    def start(self) -> None:
        """
        Runs the startup process for core
        """
        for submodule in self.config['core']['modules']['A']:
            if hasattr(self.submodules[submodule], 'start'):
                self.submodules[submodule].start()

        if is_first_boot():
            time.sleep(self.config['core']['sleep_interval'])

        for submodule in self.config['core']['modules']['B']:
            if hasattr(self.submodules[submodule], 'start'):
                self.submodules[submodule].start()
        
        while self.submodules['eps'].get_battery_bus_volts() < Power.STARTUP.value:
            time.sleep(1)
        self.mode = Mode.NORMAL

        for submodule in self.config['core']['modules']['C']:
            if hasattr(self.submodules[submodule], 'start'):
                self.submodules[submodule].start()

        for process in self.processes:
            self.processes[process].start()

        while True:
            time.sleep(1) # Keep main thread alive so that child threads do not terminate
```
{% endcode %}

