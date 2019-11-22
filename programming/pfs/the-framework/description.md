---
description: This article describes the framework pFS follows
---

# Description

## Overview

There are only two common methods that are shared among all aspects of `pFS`:

* `__init__()`
  * This is the default constructor for Python classes.
  * The constructor is used to only instantiate all objects for that class.
* `start()`
  * This method basically "starts" the specific class
  * For example, `start()` in `submodules` should start all threads and processes for that class.
  * The `start()` in `core` runs through the startup processes, and begins all application processes.

These are some important variables within `pFS`:

* `core.submodules`
  * This is a dictionary inside `core` that stores instances to all the `submodule` classes
  * Key
    * Type: str
    * e.g. "telemetry
  * Value
    * Type: Submodule
    * e.g. Telemetry
* `core.processes`
  * This is a dictionary inside `core` that stores instances to `Thread` objects that handle `core` processes
  * Read more about `core` processes [here](../core/processes.md)
* `submodule.modules`
  * This is a dictionary inside `submodule` that stores instances to all the `submodule` dependency classes
  * This dictionary is similar to `core.submodules` but does not contain references to all the `submodule` classes
* `submodule.proceses`
  * This is a dictionary inside `submodule` that stores instances to `Thread` objects that handle processes specific to that `submodule`
  * This dictionary is similar to `core.processes` but only contains `Thread` objects specific to the `submodule`
* `name`
  * This is a string that stores the name for the class
  * For core `name = "core"`
  * For a submodule\(telemetry\): `name = "telemetry"`
  * This is used in the `__str__` method
* `config`
  * This is a dictionary that stores the content in `config/config_*.yml` in a dictionary format
  * Every class\(`core` and `submodule`\) contain an instance to the same `config` variable

## Classes

`Core` is a class inside the `core/core.py` file.

`Submodule` is an abstract class inside the `submodules/submodule.py` file.

`main.py` instantiates an instance of the `Core` class and calls the `start()` method on that instance

Each specific submodule extends the `Submodule` abstract class

