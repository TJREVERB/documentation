---
description: How pFS is structured and configured
---

# Structure and Configuration

## High Level Structure

pFS is structured into two parts: [core](https://github.com/TJREVERB/pfs/tree/master/core) and [submodules](https://github.com/TJREVERB/pfs/tree/master/submodules). `core` is representative of a system while `submodules` tailor to individual hardware. An example of a `submodule` would be `aprs.py`. `aprs.py` is the `submodule` that pertains to and only to the APRS radio. The methods within `aprs.py` would pertain only to interfacing with the APRS radio so messages can be sent and received through the physical APRS radio and be processed within the `pFS` application. An example of a `core` function would be power monitoring. In flight, it is the responsibility of `core` to constantly read the current power levels of the CUBESAT and react accordingly. This functionality uses `submodules` to read the report current energy levels from the power delivery system\(EPS\). Power monitoring is not specific to any one `submodule` and affects the `pFS` application as a whole, therefore `core` is responsible for monitoring power levels.

More on `core` [here](../core/).

More on `submodules` [here](../submodules/). List of actual `submodule`below.

## Configuration

* `config/config_default.yml` and `config/config_custom.yml`

  * The `config_*.yml` files provide configuration information specific to each `submodule` and \`core\`
    * e.g. serial ports for each `submodule`\(`/dev/ttyUSB0` for `APRS`\)
    * e.g. telemetry dump intervals
    * e.g. structuring of `submodules` for startup\(`core`\)
  * `config_custom.yml`\(if present\) is given priority over `config_default.yml` 
    * `config_custom.yml` is `gitignored` and is specific to each individual developer, but `config_default.yml` provides the configuration specific to the environment on `flight-pi`a
  * Here is an excerpt of `config/config_default.yml` for the `aprs` `submodule` 



  ```yaml
  aprs:
      depends_on:
          - telemetry
      serial_port: /dev/ttyUSB0
      telem_timeout: 70
      message_spacing: 1
  ```

  * The parent key `aprs` denotes that the following values are specific to `aprs`a
    * The `depends_on` specifies a list of other `submodules` on which the parent key\(`aprs`\) needs to be able to access in flight
    * The other values\(`serial_port` ,`telem_timeout` ,`message_spacing`\) are **constants** specific to `aprs`

## Submodules

Here is a list of all the `submodules`used in `pFS` 

Everything listed here has a corresponding Python file in `pFS`

1. [antenna deployer](../submodules/antenna-deployer.md)
   1. Handles the deployment of the ISIS Antenna
2. [aprs](../submodules/radios/aprs.md)
   1. Handles the receiving and sending of messages through the APRS radio
3. [command\_ingest](../submodules/command-ingest.md)
   1. Handles the parsing and execution of command messages
4. [eps](../submodules/eps.md)
   1. Handles the interfacing between `pFS` and the Clyde Space EPS\(power management\)
5. [iridium](../submodules/radios/iridium.md)
   1. Handles the receiving and sending of messages through the Iridium radio
6. [telemetry](../submodules/telemetry.md)
   1. Handles the collection and formatting of telemetry data for the satellite as a whole

