---
description: TJREVERB 2019-20
---

# Testing

## Description

This article explains the way any type of testing\(unit testing, system testing, etc.\) is conducted with `pFS`

## Framework

Each `submodule` has its own directory under the `submodules/` directory. For example, the `aprs` submodule is located under `submodule/aprs`. Refer to [Directory Structure](pfs/directory-structure.md) for more details.

Under each `submodule` folder, there are two files:

* `__init__.py`
* `test.py`

`__init__.py` contains the class structure for the specific `submodule`

`test.py` contains unit test cases for the specific `submodule`

Under the base directory, there is a `tests.py` file. This file scraps all the `submodules` for `test.py` and, if present, runs a `run_tests` function inside each `test.py` file.

The `tests.py` file is below. View this file on [GitHub](https://github.com/TJREVERB/pfs/blob/testing/tests.py).

{% code title="tests.py" %}
```python
MODULES = {}
# Try to import test cases, ignore modules on fail
try:
    from submodules.antenna_deploy.tests import run_tests as run_antenna_tests
    MODULES["antenna"] = run_antenna_tests
except:
    MODULES["antenna"] = False
try:
    from submodules.command_ingest.tests import run_tests as run_command_tests
    MODULES["command"] = run_command_tests
except:
    MODULES["command"] = False
try:
    from submodules.eps.test import run_tests as run_eps_tests
    MODULES["eps"] = run_eps_tests
except:
    MODULES["eps"] = False
try:
    from submodules.radios.aprs_test import run_tests as run_aprs_tests
    MODULES["radio.aprs"] = run_aprs_tests
    APRS_PORT = "FAKE"
except:
    MODULES["radios.aprs"] = False
try:
    from submodules.radios.iridium_test import run_tests as run_iridium_tests
    MODULES["radios.iridium"] = run_iridium_tests
except:
    MODULES["radios.iridium"] = False
try:
    from submodules.telemetry.test import run_tests as run_telemetry_tests
    MODULES["telemetry"] = run_telemetry_tests
except:
    MODULES["telemetry"] = False

if __name__ == '__main__':
    for module, func in MODULES.items():
        if func:
            print(f"Running test cases for module {module}")
            func()
        else:
            print(f"run_tests not found for module {module}")
```
{% endcode %}

## Workflow

Checkout from the `testing` branch:

```
$ git checkout testing
```

Update from `master`

```text
$ git pull origin master
```

Add a `test.py` file to the `submodule`'s folder

{% hint style="warning" %}
Exception: the test file for `aprs` is `aprs_test.py` and `iridium_test.py` for `iridium`
{% endhint %}

{% code title="submodules/telemetry" %}
```bash
|--- __init__.py
|--- test.py #create this file
```
{% endcode %}

Run `tests.py`

```bash
$ python tests.py
```

Examine output and report and discrepancies. Report any discrepancies as Issues, with the `bug` label. Make sure to assign the developers and testers to the Issue.

