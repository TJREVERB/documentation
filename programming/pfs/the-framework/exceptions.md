---
description: This article describes how Runtime exceptions are handled in pFS
---

# Exceptions

## ModuleNotFoundError

This error extends the Python `Exception` class.

If, for any reason, a `submodule` is unable to access a reference to one of its dependencies, then it shall throw a `ModuleNotFoundError`, passing in a reference to itself and a string representation of the dependency.

The purpose for this `Exception` is, if a `submodule` is unable to access a dependency, it will `raise ModuleNotFoundError` and `core` will recognize the error, see what `submodule` it is coming    from, and see what dependency it cannot access. Afterwards, `core` will attempt to rectify the issue by resetting the erroring `submodules` `self.module` variable.

{% code title="submodules/telemetry" %}
```python
from helpers.exceptions import ModuleNotFoundError
...
raise ModuleNotFoundError(self, "aprs")
```
{% endcode %}

### Class

{% code title="helpers/exceptions.py" %}
```python
from submodules import Submodule

class ModuleNotFoundError(Exception):

    def __init__(self, submodule: Submodule, dependency: str):
        super().__init__(f"{submodule} cannot access {dependency}")

        self.submodule = submodule
        self.missing = dependency

    def get_submodule(self):
        return self.submodule

    def get_missing(self):
        return self.missing

    def __str__(self):
        return f"{self.submodule} cannot access {self.missing}"% 
```
{% endcode %}



