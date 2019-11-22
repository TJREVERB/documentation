---
description: In-flight processes handled by the core daemon
---

# Processes

## Description

These aspects of `pFS` are `Threads` that are controlled by `core` and affect `pFS` as a system.

process functions are either stored within a `submodule` or in `core/processes.py`. If the process is in `core/processes.py` that means it is not specific to any one `submodule` at all.

All `Thread` objects are managed using the `ThreadHandler` class. Read more about `ThreadHandler` [here](../structure-and-configuration/threads.md#threadhandler).

## Threads

The following processes are managed by `core` and affect `pFS` as a system.

### Power Monitoring

This process continuously queries `eps` for the current power level and switches the current `Mode` accordingly. This process switches `Mode` by calling `core.enter_normal_mode` or `core.enter_low_power_mode` respectively.

#### Power Watchdog

```python
from helpers.power import Power
from helpers.mode import Mode


def power_watchdog(core, eps) -> None:
    """
    Constantly monitors eps power levels and switches Modes accordingly
    """
    while True:
        if eps.get_battery_bus_volts() >= Power.NORMAL.value and core.state != Mode.NORMAL:
            core.enter_normal_mode(
                f'Battery level at sufficient state: {eps.get_battery_bus_volts()}')
        elif eps.get_battery_bus_volts() < Power.NORMAL.value and core.state != Mode.LOW_POWER:
            core.enter_low_power_mode(
                f'Battery level at critical state: {eps.get_battery_bus_volts()}')

```

### Telemetry Dump

This process is a `Timer` object instead of a `ThreadHandler` object. Every 60 minutes\(defined in `config_default.yml`\), this `core` `Timer` will call `telemetry.dump`. This method clears the `telemetry` message stacks and sends them through the `APRS` radio.

```python
"telemetry_dump": Timer(
    interval=self.config['core']['dump_interval'],
    function=partial(self.submodules["telemetry"].dump)
)
```

### Telemetry Heartbeat 

This process is a `Timer` object instead of a `ThreadHandler` object. Every 60 minutes\(defined in `config_default.yml` \), this `core` `Timer` will call `telemetry.heartbeat`. This method sends an "I'M ALIVE" message through the `Iridium` radio.

```python
"telemetry_heartbeat": Timer(
    interval=self.config['core']['heartbeat_invertal'],
    target=partial(self.submodules["telemetry"].heartbeat)
)
```

