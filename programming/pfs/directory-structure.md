---
description: The way pFS files are organized in the repository
---

# Directory Structure

```text
.
+-- config
|   +-- config_default.yml
+-- core
|   +-- __init__.py
|   +-- core.py
|   +-- processes.py
+-- helpers
|   +-- __init__.py
|   +-- error.py
|   +-- log.py
|   +-- mode.py
|   +-- power.py
|   +-- threadhandler.py
+-- submodules
|   +-- submodule.py
    +-- antenna_deploy
    |   +-- __init__.py
    |   +-- isisants.c
    |   +-- isisants.cpython-37m-arm-linux-gnueabihf.so
    +-- command_ingest
    |   +-- __init__.py
    +-- eps
    |   +-- __init__.py
    +-- radios
    |   +-- __init__.py
    |   +-- aprs.py
    |   +-- iridium.py
    +-- telemetry
    |   +-- __init__.py 
+-- Pipfile
+-- main.py
+-- task.py
```

