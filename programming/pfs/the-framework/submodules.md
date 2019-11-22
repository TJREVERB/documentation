---
description: This article describes how the submodules should be coded
---

# Submodules

## Framework

Each submodule should extend the `Submodule` class

```python
from submodules.submodule import Submodule


class Telemetry(Submodule):
```

The `Submodule` class defines the following methods:

* `__init__`
  * defines `self.name`, `self.modules`, `self.config`, `self.logger`, `self.processes`
  * `self.name` and `self.config` should be passed through the constructor
  * `self.modules` and `self.processes` are initialized as empty and can be overridden
  * `Submodule.__init__(self,name,config)`should be called in each `submodules` constructor
    * A `submodule`'s constructor should initialize any and all variables and processes, but it should **NOT** start any processes.
    * The constructor basically "arms" the `submodule` and makes it ready to `start`
* `start`
  * generic start method
  * The start method should initialize any runtime objects and start all processes
    * Basically tells the submodule to begin working
  * this method can, and is meant to be, overridden 
* `set_modules`
  * Accessor method for `self.modules`
  * Assigns dependencies to the `submodule`
* `has_module`
  * Checks if a `submodule` is in `self.modules`and returns `True` if present
  * Does not `raise RuntimeError`
* `get_object_or_raise_error`
  * Checks if a `submodule` is in `self.modules` and returns the module if present
  * `raise RuntimeError(f"[{self.name}]:[{module_name}] not found")`
  * The `submodule` should use this method when it needs to access an instance of one of its dependencies modules.
* `enter_normal_mode`
  * Not implemented in `Submodule`
* `enter_low_power_mode`
  * Not implemented in `Submodule`

The `Submodule` class is below. View this file on [GitHub](https://github.com/TJREVERB/pfs/blob/master/submodules/__init__.py).

{% code title="submodules/submodule.py" %}
```python
import logging


class Submodule:
    """
    Abstract class for all submodule, regardless of job
    """
    def __init__(self, name: str, config: dict):
        """
        Instantiates a new Submodule instance
        :param name: name of submodule
        :param config: dictionary of configuration data
        """
        self.name = name
        self.config = config
        self.logger = logging.getLogger(self.name)
        self.modules = dict()
        self.processes = dict()

    def start(self) -> None:
        """
        Iterates through self.processes and starts all processes.
        This method is meant to be overridden
        """

        for process in self.processes:
            self.processes[process].start()

    def enter_low_power_mode(self) -> None:
        """
        Immediately puts the submodule into a low power state. Different for each submodule.
        It is safe to assume that if this method is called, the previous state was NORMAL_MODE
        """
        raise NotImplementedError

    def enter_normal_mode(self) -> None:
        """
        Immediately puts the submodule into a normal power state. Different for each submodule.
        It is safe to assume that if this method is called, the previous state was LOW_POWER_MODE
        :return:
        """
        raise NotImplementedError

    def set_modules(self, dependencies: dict) -> None:
        """
        Accessor method for self.modules. self.modules shall be populated will any dependencies a submodule needs
        :param dependencies: Dictionary of references to other submodules that are required by the submodule
        :return: None
        """
        self.modules = dependencies

    def has_module(self, module_name: str) -> bool:
        """
        Returns True if module_name is in self.modules and its value is not None
        :param module_name: name of dependency module
        :return: bool
        """
        return module_name in self.modules and self.modules[module_name] is not None

    def get_module_or_raise_error(self, module_name: str):
        """
        Tries to access a reference to a dependency module and throws a RuntimeError otherwise
        :param module_name: name of dependency module
        :return: Reference to dependency module or None with RuntimeError raised
        """
        if module_name in self.modules and self.modules[module_name] is not None:
            return self.modules[module_name]
        else:
            raise RuntimeError(f"[{self.name}]:[{module_name}] not found")
```
{% endcode %}

