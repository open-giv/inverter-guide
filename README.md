# Inverter Compatibility Guides for GivEnergy LV Batteries

GivEnergy Low-Voltage batteries can be made to work with other makes of Inverter.

> Disclaimer: Using GivEnergy battery packs with third-party inverters is at your own risk. This information is provided on a best-effort basis to help extend the life of out-of-support hardware. **No responsibility is accepted for any damage caused by following this documentation.** This document may be incomplete or inaccurate. All configurations, settings, and advice should be reviewed by a competent person.

This guide documents known / proven interoperability.  Inverter support is divided into three types:
* Direct support (configure the inverter with generic battery settings)
* Briged support (which requires technology to act as a 'middle-man' between the batteries and inverter)
* Explicit support (does not exist today, and likely may never exist)

## Direct support

Currently, direct support treats the GivEnergy batteries as generic 51.2V LiFePo battery packs.  The obvious (biggest) benefit of direct support is no requirement for additional tech - just the inverter and the batteries.

The downside is that the inverter has no true idea of the battery charge level or alarms.  This means all charging and discharging is controlled by specifying voltage levels.

The inverter is entirely unaware of the health of the battery, and without any other visibility into battery health you're effectively flying blind should the BMS enter a fault state.

The following inverters are currently documented:

| Make | Models | Status |
|-|-|-|
|Solis|[S6-EH1P-{X}K-L-PLUS](./solis-direct/s6-eh1p-xk-l-plus.md)|Preliminary|


## Bridged support

With bridged support a device mediates between the inverter and the battery packs.  The advantage of bridged support is that basic health status information is available via the inverter, often:
* Alarm states
* Cell temperatures
* Cell min/max voltages
* Battery state of health (capacity as a percentage of original capacity)

This visibility enables some basic insight into the health of the battery, in addition the bridge may expose greater insight into the pack status, such as pack-by-pack health and cell-by-cell voltages.

Bridged support works by translating the GivEnergy BMS protocol into one natively supported by the inverter.  The most common for this is the Pylon Tech protocol, which is described in several locations on the Internet.

The following inverters are currently documented.

| Make | Models | Bridge | Status |
|-|-|-|-|
|Solis|[S6-EH1P-{X}K-L-PLUS](./pylontech-compatible/s6-eh1p-xk-l-plus.md)|[go-bridge](../go-bridge/)|Preliminary|


## Explicit support

It may be the case that in future 3rd-party inverters directly support the GivEnergy BMS protocol, but that is not the case today.
