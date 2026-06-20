> Disclaimer: Using GivEnergy battery packs with third-party inverters is at your own risk. This information is provided on a best-effort basis to help extend the life of out-of-support hardware. **No responsibility is accepted for any damage caused by following this documentation.** This document may be incomplete or inaccurate. All configurations, settings, and advice should be reviewed by a competent person.

# Solis S6-EH1P-{X}K-L-PLUS

This range of hybrid inverters supports generic LiFePo 51.2V batteries.

## Topology

This guide assumes that if you have multiple batteries, they are connected in a daisy chain (as is conventional for GivEnergy battery packs), not in parallel via a busbar.

GivEnergy "plug-to-lug" cables are compatible with the battery connections on Solis inverters. The lugs are 8 mm (M8).

If you do not have a "plug-to-lug" cable, you can cut down a "plug-to-plug" cable and crimp on 16 mm2 M8 bolt lugs. Use an appropriate crimp tool, as connection quality is critical for safety.

## Configuration

In the inverter battery configuration screen, select `51.2V Lithium Battery(Without COMM)`.

### Screen 1/2
![screen1](./s6-eh1p-xk-l-plus-cfg-screen1.jpg "Screen 1/2")

Then configure the detailed settings:

| Setting | Recommended Value | Notes |
| - | - | - |
| Max charge current | 70 A | Consider both the documented battery limit and the current-carrying capacity of the battery cable. |
| Max discharge current | 70 A | Consider both the documented battery limit and the current-carrying capacity of the battery cable. |
| Over discharge | TBD | |
| Recovery | TBD | |
| Force charge | TBD | |

### Screen 2/2
![screen2](./s6-eh1p-xk-l-plus-cfg-screen2.jpg "Screen 2/2")

| Setting | Recommended Value | Notes |
| - | - | - |
| Batt capacity | {calculate} | Total capacity of all battery packs connected in parallel. |
| Equalizing charge voltage | TBD | |

## Home Assistant

If you currently use GivTCP with Home Assistant to monitor or manage your GivEnergy system, there are good alternatives for Solis inverters.

This [Solis integration](https://github.com/Pho3niX90/solis_modbus) has been seen to work well.

If you are using Home Assistant to control the inverter, it is recommended to increase the data logger "number of clients" setting.

In the SolisCloud app:
`Device` > `Datalogger` > `Hamburger Menu` > `Datalogger Control` > `Max. Number of Connections Setting`

A value of `3` seems to work well.

## Real-time controls

Solis inverters, like GivEnergy models, can be susceptible to premature failure if automated scripts make frequent configuration changes. Similar to recent GivEnergy firmware, recent Solis inverters support "real-time controls" that avoid this issue.

Real-time controls override the inverter's default behavior. They are designed for frequent updates by an Energy Management System (EMS). Settings must be refreshed regularly, or the inverter will automatically revert to its default behavior.

Below is an example automation that runs once per minute (on second `21`). If battery voltage is below `55 V` and the current time is within the Octopus Go off-peak window, it:

1. Sets the real-time control timeout to `2` minutes.
2. Sets charge power to `3 kW`.
3. Writes `1` to the `RC Force Charge/Discharge` Modbus register (`43135`).

Because the automation runs every minute, it maintains continuous charging during the off-peak window until the battery reaches `55 V`.

```yaml
alias: Charge Battery
description: ""
triggers:
  - trigger: time_pattern
    seconds: "21"
conditions:
  - condition: numeric_state
    entity_id: sensor.solis_battery_voltage
    below: 55
  - condition: time
    after: "00:30:00"
    before: "05:30:00"
    weekday:
      - sun
      - mon
      - tue
      - wed
      - thu
      - fri
      - sat
actions:
  - action: number.set_value
    metadata: {}
    target:
      entity_id: number.solis_rc_timeout
    data:
      value: "2"
  - action: number.set_value
    metadata: {}
    target:
      entity_id: number.solis_rc_force_battery_charge_power
    data:
      value: "3000"
  - action: solis_modbus.solis_write_holding_register
    metadata: {}
    data:
      host: XXX.XXX.XXX.XXX
      address: 43135
      value: 1
mode: single

```


## Known Issues

### Scheduled Charging

The scheduled charging and discharging features of the inverter work, with a major caveat. For each scheduled charge window, a maximum voltage must be specified. Charging stops when this voltage is reached. The current maximum value is `52.5 V`, which is less than half charge for GivEnergy batteries.

#### Work-arounds

1. Fully charge the batteries from solar, if this fits your use case.
2. Use Home Assistant automations to trigger charging via real-time controls (see example above).

