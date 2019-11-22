---
description: TJREVERB 2019-20
---

# Overview

## Description

The flight software for the TJREVERB 2019-20 mission is called pFS \(Python Flight Software\). The code is 100% open-source and stored on [GitHub](https://github.com/TJREVERB/pfs). The language is Python 3.7.

The Python Flight Software\(pFS\) application is the code that controls the behavior of the CUBESAT in flight. It is designed to run in a Linux environment, specifically on a Debian-based system such as the Raspberry Pi SBC. The TJREVERB CUBESAT will utilize a[ Raspberry Pi Zero](https://www.adafruit.com/product/2885?gclid=Cj0KCQjwh8jrBRDQARIsAH7BsXfBCHQ4I2ZRvbNOm1It2Jvz6TT1MAxGhD57qJz_Nlmmx8QzQ9cRq4YaAm0mEALw_wcB) to control the satellite and run pFS.

## Code

pFS is open source and maintained at [https://github.com/TJREVERB/pfs](https://github.com/TJREVERB/pfs). pFS is meant to be run in a \*nix environment, so any Linux distribution or macOS would work fine, in theory. Although possible, running pFS on Windows or any other operating system would be incredibly inefficient and is not supported.

## Hardware

* APRS
  * Automatic Packet Reporting System. Space-tested, _reliable_ radio
  * [APRS](http://www.aprs.org/)
* Iridium
  * Iridium Satellite Communications radio. Untested radio
  * [Iridium](https://www.iridium.com/)
* EPS
  * Electronic Power System. Power management system for the satellite
* Raspberry Pi Zero Model W
  * Flight Computer. Runs the flight software application
  * [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-zero-w/)





