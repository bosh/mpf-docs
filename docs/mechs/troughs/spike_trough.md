---
title: Using the Stern Spike Trough
---

# Using the Stern Spike Trough


Related Config File Sections:

* [ball_devices:](../../config/ball_devices.md)
* [playfields:](../../config/playfields.md)
* [spi_bit_bang:](../../config/spi_bit_bang.md)
* [digital_outputs:](../../config/digital_outputs.md)

Unlike other troughs the Stern Spike trough contains an 74HCT165 chip
and is interfaced via SPI. This is a problem if your platform is not
using SPI to read switches (which most platforms are not). If you are on
[Stern Spike](../../hardware/spike/index.md) then
just configure your trough as described in
[How to configure a modern trough with opto switches](modern_opto.md).

!!! note

    While the Stern Spike trough works with other platforms we do not
    recommend to buy it if you are not using the Stern Spike platform.
    Instead, if you did not yet buy a trough buy one with normal switches or
    optos (unless you are using Stern Spike). This will make your life
    easier.

## Part numbers:

* Transmitter: 520-5344-00
* Receiver: 520-5345-00/520-5345-01

## Config (if you are not on Stern Spike):

If you got a Stern Spike trough but are not using
[Stern Spike](../../hardware/spike/index.md) you
can use our
[SPI Bit Bang platform](../../hardware/spi_bit_bang.md) to read the switches of your trough:

``` yaml
hardware:
  platform: your_platform, spi_bit_bang      # add your platform first here
spi_bit_bang:
  miso_pin: s_miso
  cs_pin: o_cs
  clock_pin: o_clock
digital_outputs:
  o_cs:
    number: 1        # adjust this for your platform
    type: driver
  o_clock:
    number: 2        # adjust this for your platform
    type: driver
switches:
  s_trough0:
    number: 0
    platform: spi_bit_bang
  s_trough1:
    number: 1
    platform: spi_bit_bang
  s_trough2:
    number: 2
    platform: spi_bit_bang
  s_trough3:
    number: 3
    platform: spi_bit_bang
  s_trough4:
    number: 4
    platform: spi_bit_bang
  s_trough5:
    number: 5
    platform: spi_bit_bang
  s_trough6:
    number: 6
    platform: spi_bit_bang
  s_trough_jam:      # this might be also number 0
    number: 7
    platform: spi_bit_bang
  s_miso:
    number: 10       # adjust this for your platform
  s_plunger:
    number: 11       # adjust this for your platform
# the following is the same as in the "modern trough with opto switches" tutorial
coils:
  c_trough_eject:
    number: 4
    default_pulse_ms: 20
ball_devices:
  bd_trough:
    ball_switches: s_trough1, s_trough2, s_trough3, s_trough4, s_trough5, s_trough6, s_trough_jam
    eject_coil: c_trough_eject
    tags: trough, home, drain
    jam_switch: s_trough_jam
    eject_coil_jam_pulse: 15ms
    eject_targets: bd_plunger
    eject_timeouts: 3s
  bd_plunger:
    ball_switches: s_plunger
    mechanical_eject: true
    eject_timeouts: 5s
playfields:
  playfield:
    default_source_device: bd_plunger
    tags: default
```

## What if it doesn't work?

Have a look at our
[troubleshooting guide for ball_devices](../ball_devices/troubleshooting.md).
