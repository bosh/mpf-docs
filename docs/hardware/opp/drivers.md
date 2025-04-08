---
title: OPP coils / drivers
---

# OPP coils / drivers


Related Config File Sections:

* [coils:](../../config/coils.md)
* [opp_coils:](../../config/opp_coils.md)

There are a few things to know about controlling drivers and coils with
OPP hardware.

--8<-- "common_ground_warning.md"

## Number

OPP coils are numbered sequentially depending on which wing board is the
coil output. Wing position 0 contains coil numbers 0 to 3. Wing position
1 contains coil numbers 4 to 7. Wing position 2 contains coil numbers 8
to 11. Wing position 3 contains coil numbers 12 to 15. The coil is
numbered using the position of the OPP card (starting at 0), then a
'-', and finally the coil number on the card.

``` yaml
coils:
  some_coil:
    number: 0-12
```

The above example configures a coil output as the first OPP card, and
the third wing board, first output. On the microprocessor card, the
output is marked as 3.4 (wing port 3, position 4).

## Pulse time

The OPP hardware also has the ability to specify the "pulse time".
Pulse time is the coil's initial kick time. For example, consider the
following configuration:

``` yaml
coils:
  some_coil:
    number: 0-12
    default_pulse_ms: 30
```

When MPF sends this coil a pulse command, the coil will be fired for
30ms.

## Hold Power

If you want to hold a driver on at less than full power, MPF does this
by using *default_hold_power* parameter which works for all platforms.
It can range from 0.0 to 1.0 and defines the time share the coil is on
(0%-100%).

The period is fixed at 16ms for OPP. To set the hold power to 25%, set
default_hold_power to .25 and OPP will use 4ms/16ms = 25%.

``` yaml
coils:
  some_coil:
    number: 0-3
    default_pulse_ms: 32
    default_hold_power: 0.5
```

This will configure OPP card 0, solenoid wing 0, last solenoid to have
an initial pulse of 32 ms, and then be held on at 50% power.

## Pulse Power

!!! note

    This feature is only available on OPP firmware version 2.3.0.5 and
    above.

If you want to pulse a driver on at less than full power, MPF does this
by using *default_pulse_power* parameter. This can be used along with
default_pulse_ms to fully tune the response of a coil. This can be
especially useful when controlling lower voltage coils from a 48V
source.

It can range from 0.0 to 1.0 and defines the time share the coil is on
(0%-100%). Any value under 0.03125 will be forced to output 3.125%.

``` yaml
coils:
  some_coil:
    number: 0-3
    default_pulse_ms: 32
    default_pulse_power: 0.8125
    default_hold_power: 0.125
```

This will configure OPP card 0, solenoid wing 0, last solenoid to have
an initial pulse of 32 ms at 81.25% power, and then be held on at 12.5%
power.

## Recycle Factor

OPP allows you to fine tune the
[recycle time of your coils](../../mechs/coils/recycle.md). If you add `recycle: True` to your coil you can set
`recycle_factor` in the `platform_settings` secton of your coil to set
the recycle time. The time will be `default_pulse_ms * recycle_factor`.
For instance, if you set a pulse time of `10ms` and a recycle_factor of
two the coil will cool down for at least `20ms`. This is an example:

``` yaml
coils:
  some_coil:
    number: 0-3
    default_pulse_ms: 10
    default_recycle: true
    platform_settings:
      recycle_factor: 2
```

### What if it did not work?

Have a look at our
[OPP troubleshooting guide](../../troubleshooting/index.md).

### Related How To guides

* [Coil Resistance and Hardware Details](../../mechs/coils/index.md)
* [Wiring Dual Wound Coils](../../mechs/coils/dual_wound_coils.md)
* [Dual-Wound versus Single-Wound coils](../../mechs/coils/dual_vs_single_wound.md)
* [Adjust coil hold power](../../mechs/coils/hold_power.md)
* [Adjust coil strength (pulse times)](../../mechs/coils/pulse_power.md)
* [Recycle / "Cool Down" Time](../../mechs/coils/recycle.md)
* [Details About Flippers](../../mechs/flippers/index.md)
* [How to configure single-wound flippers](../../mechs/flippers/single_wound.md)
* [How to configure dual-wound flippers](../../mechs/flippers/dual_wound.md)
* [Flipper end-of-stroke (EOS) switches](../../mechs/flippers/eos_switches.md)
