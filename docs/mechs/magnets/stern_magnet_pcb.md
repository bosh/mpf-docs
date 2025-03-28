---
title: How to use the Stern Magnet Processor Board
---

# How to use the Stern Magnet Processor Board


Related Config File Sections:

* [digital_outputs:](../../config/digital_outputs.md)
* [switches:](../../config/switches.md)
* [shows:](../../config/shows.md)
* [show_player:](../../config/show_player.md)

Stern uses a Magnet Processor Board (part number #520-6801-00) in
Metallica for the coffin under the hammer. This board is special as it
can detect the ball a ball near the magnet. In addition in can grab and
hold the ball.

## Connecting the Board

[TODO: Add an image of the magnet processor board.](../../about/help.md)

### Supply Voltage

The itself magnet runs on 50VDC and the board needs a logic supply of
5VDC. Both of the ground rails seem do be isolated from each other and
are not connected on the MPB itself. However, you need to connect those
two GNDs to maintain common ground (see
[common ground](../../hardware/voltages_and_power/voltages_and_power.md) for details).

### Zero Crossing

The MPB has a 13VAC input on J1-8 which seems to be unused and is not
needed for its operation.

### Inputs and Outputs

There are 3 logic level (5V) inputs and 1 logic level output on the MPB.
The inputs are called DATA6 (J1-5), DATA7 (J1-6) and STROBE (J1-7) on
the MPB and are used to control the mode of operations of the MPB. The
single output of the MPB is called Sw Drive (J1-4). This changes from
low to high with ball detection. To enable the output to pass sufficient
current to power an opto isolator (recommended) or to use it in a switch
matrix, enable the output with +5V at Sw Return (J1-3).

## Controller States

So let's go over the four states of the controller:

### OFF:

Signals: D6 = low, D7 = low, pulse Strobe

The magnet does nothing.

### GRAB:

Signals: D6 = high, D7 = low, pulse Strobe

The magnet activates for a second and then deactivates. This grabs the
ball from centimeters away. GRAB is a one time event. The controller
does not GRAB again unless it passes through another state or OFF- state
first.

### DETECT:

Signals: D6 = high, D7 = high, pulse Strobe

The magnet is pulsed with 4µs peaks to detect the ball. Ball detection
requires a small distance of seperation between the magnet core and the
ball. 3.5 mm of wood appears to be a compromize between detection and
holding strength. The green LED on the MPB lights up when the ball is
detected and the output pin 4 goes high.

### HOLD(+DETECT):

Signals: D6 = low, D7 = high, pulse Strobe

The magnet is pulsed with three 4µs peaks (presumably) for detection and
a longer pulse (presumably) for holding the ball on the magnet. The ball
is held firmly, but the detection does not always work. Sometimes it was
very reliable, sometimes there was no detection at all. This might
require some more analysis.

## Config

To use the magnet in MPF you can use the following config as starting
point. Shows that pulse the input pins are run as a sequence of events,
not as loops. The board will maintain its mode until instructed to
change. Caution: if the board is in a hold mode and MPF crashes the
board will remain in the hold mode until power is lost or MPF instructs
it to enter the OFF-state.

``` yaml
digital_outputs:
  magnet_strobe:
    number: 1    # number depends on your platform
    type: driver
    enable_events: magnet_strobe_on
    disable_events: shutdown, magnet_strobe_off
  magnet_d6:
    number: 2    # number depends on your platform
    type: driver
    enable_events: magnet_d6_on
    disable_events: shutdown, magnet_d6_off
  magnet_d7:
    number: 3    # number depends on your platform
    type: driver
    enable_events: magnet_d7_on
    disable_events: shutdown, magnet_d7_off

switches:
  s_detect:
    number: 1     # number depends on your platform

shows:
  magnet_state_off:
    - time: 0
      events:
        - magnet_d6_off
        - magnet_d7_off
    - time: 20ms
      events:
        - magnet_strobe_on
    - time: 30ms
      events:
        - magnet_strobe_off
    - time: 50ms
      events:
        - magnet_d6_off
        - magnet_d7_off
  magnet_state_detect:
    - time: 0
      events:
        - magnet_d6_on
        - magnet_d7_on
    - time: 20ms
      events:
        - magnet_strobe_on
    - time: 30ms
      events:
        - magnet_strobe_off
    - time: 50ms
      events:
        - magnet_d6_off
        - magnet_d7_off
  magnet_state_grab:
    - time: 0
      events:
        - magnet_d6_on
        - magnet_d7_off
    - time: 10ms
      events:
        - magnet_strobe_on
    - time: 20ms
      events:
        - magnet_strobe_off
    - time: 50ms
      events:
        - magnet_d6_off
        - magnet_d7_off
  magnet_state_hold:
    - time: 0
      events:
        - magnet_d6_off
        - magnet_d7_on
    - time: 20ms
      events:
        - magnet_strobe_on
    - time: 50ms
      events:
        - magnet_strobe_off
    - time: 70ms
      events:
        - magnet_d6_off
        - magnet_d7_off
```

You can then turn the controller into `detect` in a mode by posting the
`magnet_state_detect` event. Then add an event_player based on
`s_detect_active` to turn the controller into the `grab` state. Finally,
after a few seconds turn it into the `hold` state and check the state of
`s_detect` to see if the grab succeeded.

[TODO: Add some example config for this logic.](../../about/help.md)
