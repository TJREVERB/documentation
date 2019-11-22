---
description: What pFS should be able to do in-flight conceptually
---

# Conceptual Operations

## General Aspects of pFS

The purpose of the 2019-20 TJREVERB mission is to test the usability of [Iridium](https://www.iridium.com/) radio in Low Earth Orbit. Since the Iridium radio is not verified to work in LEO, another radio called the APRS radio is used as a control group. All critical messages from the ground station to the satellite are sent through the APRS radio. 

[Critical Messages](./#critical-messages) include: 

* Telemetry dumps
* Command Execution messages
* APRS echos 

To test the usability of the Iridium radio, all non critical messages are sent through the Iridium radio.

[Non Critical Messages](./#non-critical-messages) include:

* Heartbeat messages

### Critical Messages

* Telemetry Dumps
  * On a set interval, the satellite will send a collection of all the data it has collected through APRS
* APRS echos
  * The physical APRS radio sends automatic echo messages to the ground station. This aspect is not controlled by the flight software whatsoever
* Command Execution
  * The TJREVERB groundstation should be able to send a message to the satellite via the APRS radio, containing a message which describes a specific function the flight software should execute.
    * If the most recent telemetry data is required by the groundstation, the TJREVERB groundstation should be able to send a message via the APRS radio telling the satellite to trigger a telemetry dump regardless of the interval. The flight software should receive such a message through the APRS radio, recognize the message as a command, and follow through with the execution of that command

### Non Critical Messages

* Heartbeat messages
  * On a set interval, the satellite will send a basic message\(i.e 'I'M ALIVE'\) through the Iridium radio for the sake of using the iridium radio 

## Startup

This is how `pFS` should behave when `flight-pi` boots up for the first time.

1. When the flight computer\(`flight-pi`\) first boots, the computer shall run the `main.py` file to begin the initialization of `core`
2. When `core` is initialized, it shall read from the `config` and initialize all `submodules` regardless of order. In addition it shall initialize all other required variables.
3. After `core` is initialized, `main.py`shall call the `start()` method in core which will begin the startup sequence
4. In the startup sequence, `core` shall call the `start()` method for all the `submodules` listed under ‘A’ `submodules`. Afterwards, `core` shall determine if it is booting up for the first time and sleep for 30 minutes if it is the first boot before initializing ‘B’ and ‘C’ `submodules`. If it is not the first boot, `core` shall ignore the 30 minute sleep period
5. After starting all `submodules`, `core` shall iterate through its `processes`and start all of its `processes`

Here is an excerpt of `config/config_default.yml` that outlines the startup process for `core`:

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
```
{% endcode %}

## Command Ingest

**It would be helpful to read about** [**message formatting**](../formats-and-commands.md) **before reading this section**

1. When APRS receives a message from the ground station, it shall send the message to `telemetry` for processing
2. Once `telemetry` determines the message is of type `Command`, it shall send send the message to `command_ingest`
3. Once `command_ingest` encounters the message, it shall parse the message and determine the corresponding function to execute and then execute it
4. On a successful execution, `command_ingest` shall send a message through APRS that a `command` was executed successfully
5. On an unsuccessful execution, `command_ingest` shall send a message through APRS that a `command` was not executed successfully.

## Dumps and Heartbeats

On a set interval, the satellite shall send data dumps and heartbeat messages to deliver data to the groundstation. 

### Telemetry Dump

Every hour, the satellite shall send all telemetry data collected through the `aprs` radio. The satellite shall purge the data collected once it has been sent to the groundstation. This dump message is flagged as a Critical Message.

### Telemetry Heartbeat

Every hour, the satellite shall send a simple, "I'M ALIVE", message through the `iridium` radio. This heartbeat message is flagged as a Non Critical Message.

## Modes

{% page-ref page="modes.md" %}



