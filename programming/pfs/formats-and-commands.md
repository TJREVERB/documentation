---
description: >-
  The standardization of all messages and commands along with how they are
  processed
---

# Formats and Commands

## Overview

From a software standpoint, there are three types of messages sent, both inside satellite and between the groundstation and the satellite.

Types of Messages:

* [Log](formats-and-commands.md#logs)
* [Errors](formats-and-commands.md#errors)
* [Commands](formats-and-commands.md#commands)

## Logs

`Log` messages include any informative, non-error message. Since there is not a use case where the TJREVERB groundstation will be sending a message of type `Log` to the satellite, `Logs` can be represented by a `pFS` Python class.

### String Representation Format

This is how `Log` is represented through a `string`

* PREFIX
  * `LOG&`
* BODY
  * `{SYSTEM}:{LEVEL}:{TIMESTAMP}:{MESSAGE}`

Example: `LOG%CORE:INFO:00000:STARTUP`

This is the `Log` class. This class is also on [GitHub](https://github.com/TJREVERB/pfs/blob/master/helpers/log.py).

{% code title="helpers/log.py" %}
```python
from datetime import datetime


class Log():
    """
    A class representing log messages.
    """

    def __init__(self, sys_name: str = 'CORE', lvl: str = 'INFO', ts: datetime = datetime.utcnow(), msg: str = None):
        """
        Constructor.
        :param sys_name: Subsystem name
        :param lvl: Level of message (info, warning, error)
        :param ts: Timestamp in datetime format.
        :param msg: String message
        """
        self.system = sys_name
        self.level = lvl
        self.timestamp = ts
        self.message = msg
        self.header = 'LOG&'

    def __str__(self):
        """
        :return: String representation of log message.
        """
        return "{0}:{1}:{2}:{3}:{4}".format(self.header, self.system, self.level,
                                            self.timestamp.strftime("%Y/%m/%d@%H%M%S"), self.message)
```
{% endcode %}

To import this class from a `submodule`, add this line to the `submodule`file

```python
from helpers import log # initialization will be log.Log(..)
#-- OR --
from helpers.log import Log # initializationinitialization will be Log(..)
```

## Errors

`error` messages include any message that denotes an error in the flight software. For example, if APRS was unable to send a message for any reason, the resultant message would be logged as a message of type `error`. Since there is no use case where the TJREVERB groundstation will be sending a message of type `error` to the satellite, `errors`can be represented by a `pFS`Python class.

### String Representation Format

This is how `Error` is represented through a `string`

* PREFIX
  * `ERR!`
* BODY
  * `{SYSTEM}:{TIMESTAMP}:{MESSAGE}`

Example: `ERR!CORE:00000:STARTUP_FAILED`

This is the `error` class. This class is also on [GitHub](https://github.com/TJREVERB/pfs/blob/master/helpers/error.py).

{% code title="helpers/error.py" %}
```python
from datetime import datetime


class Error():
    """
    A class representing error messages.
    """

    def __init__(self, sys_name='CORE', ts: datetime = datetime.utcnow(), msg: str = None):
        """
        Constructor.
        :param sys_name: The name of the subsystem.
        :param ts: A timestamp in datetime format.
        :param msg: A string representing the message.
        """
        self.system = sys_name
        self.timestamp = ts
        self.message = msg
        self.header = 'ERR!'

    def __str__(self) -> str:
        """
        :return: A string representation of this error.
        """
        return "{0}:{1}:{2}:{3}".format(self.header, self.system,
                                        self.timestamp.strftime("%Y/%m/%d@%H.%M.%S"), self.message)
```
{% endcode %}

To import this class from a `submodule`, add this line to the `submodule`file:

```python
from helpers import error # initialization will be error.Error(..)
#-- OR --
from helpers.error import Error # initialization will Error(..)
```

## Commands

`command` messages include any message that denotes an intent to execute command on the satellite. For example, if it was necessary for the groundstation to receive the most recent telemetry data dump, the groundstation could send a message via the APRS radio to the satellite. The satellite, upon receiving the message, shall recognize the message as a `command`, and, if valid, execute the `command`. Since there is a use case where the groundstation will be sending messages of type `command` to the satellite, there needs to be a way of representing `commands` with `strings`.

#### String Representation Format

* Prefix
  * `CMD$`
* Body
  * `[module_name];[function_name];[arg1,arg2,arg3,..]`

There is no `pFS` Python class representation for `commands`

