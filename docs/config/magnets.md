---
title: "magnets: Config Reference"
---

# magnets: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `magnets:` section of your machine config is used to define magnet
mechanisms from coils and (optionally) switches. There are settings that
control the timing of grabbing, releasing, and "flinging" the ball.

## Required settings

The following sections are required in the `magnets:` section of your
config:

### magnet_coil:

Single value, type: `string` name of a [coils:](coils.md) device. Defaults to empty.

Note that is any of the magnet activation times are longer than 255ms
and the magnet pulse power is 100%, then you will need to add
`allow_enable: true` to the coil's entry in the `coils:` section of the
machine config.

## Optional settings

The following sections are optional in the `magnets:` section of your
config. (If you don't include them, the default will be used).

### disable_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Default: `ball_will_end, service_mode_entered`

These events mean the magnet will no longer try to grab a ball if the
`grab_switch:` is activated.

### enable_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Default: `ball_started`

These events enable the magnet to grab a ball based on the
`grab_switch:` being activated.

### fling_ball_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

Events to trigger flinging a ball.

### fling_drop_time:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `250ms`

How long the magnet is deactivated for before the "fling_regrab_time"
when it's flinging a ball.

### fling_regrab_time:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `50ms`

How long the "second" (fling) pulse is for when a magnet is flinging a
ball after its dropped it.

### grab_ball_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

These events cause the magnet to immediately attempt to grab a ball. The
magnet will be activated for the `grab_time:`.

### grab_switch:

Single value, type: `string` name of a
[switches:](switches.md) device. Defaults to
empty.

The switch which activates grabbing a ball.

### grab_time:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `1.5s`

How long the magnet will be energized when attempting to grab a ball.

### playfield:

Single value, type: `string` name of a
[playfields:](playfields.md) device. Default:
`playfield`

The playfield on which this magnet is.

### release_ball_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

These events cause the magnet to deactivate for the `release_time:`
setting.

### release_time:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `500ms`

How long the magnet disables to release a ball.

### reset_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Default: `machine_reset_phase_3, ball_starting`

These events release a grabbed ball and disable the magnet.

### console_log:

Single value, type: one of the following options: none, basic, full.
Default: `basic`

Log level for the console log for this device.

### debug:

Single value, type: `boolean` (`true`/`false`). Default: `false`

Set this to true to see additional debug output. This might impact the
performance of MPF.

### file_log:

Single value, type: one of the following options: none, basic, full.
Default: `basic`

Log level for the file log for this device.

### label:

Single value, type: `string`. Default: `%`

Name of this device in service mode.

### tags:

List of one (or more) values, each is a type: `string`. Defaults to
empty.

A list of tags. Not used for any logic.

## Related How To guides

* [Magnets](../mechs/magnets/index.md)
