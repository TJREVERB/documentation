---
description: Description of the configuration file
---

# Configuration

## Configuration

The configuration file syntax is YAML. Learn more about YAML [here](https://learn.getgrav.org/16/advanced/yaml).

Each `submodule` and `core` has a key at the parent level. All children values are specific to that parent. 

Here are the important aspects of`config.yml`

* Core
  * `core/modules` is a list of lists
    * `core/modules/A` is a list of submodule names that are essentially APIs and can be started without a requisite amount of power.
    * `core/modules/B` is a list of submodule names that only need to be started once
      * `antenna_deployer` is the only submodule listed under 'B' for `antenna_deployer` only needs to be started on the first boot of the satellite
    * `core/modules/C` is a list of submodule names that can only be started after a requisite amount of power has been determined
  * `core/dump_interval` time interval\(in seconds\) on which `core` initializes a telemetry dump
  * `core/sleep_interval` time interval\(in seconds\) on which `pFS` has to sleep and conduct no operations under mandatory regulations
* `[submodule_name]/depends_on`
  * `depends_on` contains a list of submodule names on which the parent `submodule` needs to have a reference to
    * For example, `telemetry` needs to have a reference to `aprs` so that it can send messages. Therefore `aprs` is under `telemetry/depends_on`
    * Notice that:
      * All submodules require a reference to `telemetry`
      * The `command_ingest` submodule has a reference to all other `submodules`

{% code title="config/config\_default.yml" %}
```yaml
core:
    modules:
        A:
            - eps
            - command_ingest
        B:
            - antenna_deployer
        C:
            - aprs
            - iridium
            - telemetry
    dump_interval: 3600
    heartbeat_interval: 3600
    sleep_interval: 1800

antenna_deployer:
    depends_on:
        - telemetry
    ANT_1: 0
    ANT_2: 1
    ANT_3: 2
    ANT_4: 3
aprs:
    depends_on:
        - telemetry
    serial_port: /dev/ttyUSB0
    telem_timeout: 70
    message_spacing: 1
command_ingest:
    depends_on:
        - antenna_deployer
        - aprs
        - eps
        - iridium
        - telemetry
eps:
    depends_on:
        - telemetry
    looptime: 20
iridium:
    depends_on:
        - telemetry
    serial_port: /dev/ttyUSB0
telemetry:
    depends_on:
        - command_ingest
    buffer_size: 100
    max_packet_size: 170
```
{% endcode %}

