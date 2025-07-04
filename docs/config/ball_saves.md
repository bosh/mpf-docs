---
title: "ball_saves: Config Reference"
---

# ball_saves: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**YES** :white_check_mark:|

The `ball_saves:` section of your config is where you create [ball save
devices](#). Here's an example:

``` yaml
ball_saves:
  default:
    active_time: 10s
    hurry_up_time: 2s
    grace_period: 2s
    enable_events: mode_base_started
    timer_start_events: balldevice_plunger_lane_ball_eject_success
    auto_launch: true
    balls_to_save: 3
    debug: true
```
Note that the total time starts once on the very first ball and is not being reset on a ball drain. In the above example that means that you have three ball saves
within 12 seconds (10s active time plus 2s grace period). If your first ball drains after 10s, you get a ball save, if the next ball drains again after another
10s you do *not* get a second ball save because the timer is not reset and a total of 20s has elapsed.

## Optional settings

The following sections are optional in the `ball_saves:` section of your
config. (If you don't include them, the default will be used).

### active_time:

Single value, type: `time string (secs) or template`
([Instructions for entering time strings](instructions/time_strings.md) and
[Instructions for entering templates](instructions/dynamic_values.md)). Default: `0`

How long the ball save is active (in MPF time string format) once it
starts counting down. This includes the *hurry_up_time,* but does not
include the *grace_period* time. Leave this setting out (or set it to 0)
for unlimited time. Default is *0*.

### auto_launch:

Single value, type: `boolean` (`true`/`false`). Default: `true`

True/False which controls whether the ball save should auto launch the
saved ball or wait for the player to launch it.

### ball_locks:

List of one (or more) values, each is a type: string name of a
[ball_devices:](ball_devices.md) device.
Defaults to empty.

Use those devices first when ejecting balls to the playfield on ball
save. If there are not enough balls in the lock more balls will be
requested to the source_playfield.

### balls_to_save:

Single value, type: `integer`. Default: `1`

How many balls this ball saver should save before disabling itself. Set
it to -1 for unlimited.

### delayed_eject_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

Delay the eject until a event from `delayed_eject_events` is posted. For
instance, this can be used in combination with `only_last_ball` at the
end of a wizard mode to drain all balls and continue the game later.

### disable_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Default: `ball_will_end, service_mode_entered`

Event(s) which disable this ball save, meaning a drained ball will no
longer be saved.

### early_ball_save_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

Event(s) which will trigger a ball save to take place before the current
ball has drained. A typical example of this might be switch activation
events from outlane switches which can be used to trigger a ball save as
soon as the ball hits the outlane.

### eject_delay:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `0`

Delay the eject of the new ball for `eject_delay` ms. This might be
useful if you want to play a show or some sounds first for dramatic
reasons.

### enable_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

Event(s) which enable this ball save. This also starts the ball save
timer running unless there are optional timer_start_events present, see
below.

### grace_period:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `0`

The "secret" time (in MPF time string format) the ball save is still
active. This is added onto the *active_time*. Default is *0*.

### hurry_up_time:

Single value, type: `time string (ms)`
([Instructions for entering time strings](instructions/time_strings.md)). Default: `0`

The time before the ball save ends (in MPF time string format) that will
cause the *ball_save_\(name\)_hurry_up* event to be posted. Use this
to change the script for the light or trigger other effect. Default is
*0*.

### only_last_ball:

Single value, type: `boolean` (`true`/`false`). Default: `false`

Only save the last ball. In case two balls are in play and only one
drains it will not be saved.

### source_playfield:

Single value, type: `string` name of a
[ball_devices:](ball_devices.md) device.
Default: `playfield`

Playfield to eject the saved balls to.

### timer_start_events:

List of one (or more) device control events
([Instructions for entering device control events](instructions/device_control_events.md)). Defaults to empty.

Events in this list, when posted, start this ball saver's countdown
timer provided the ball save has been enabled, above. This allows the
timer to be started separate from the save being enabled. For example, a
light might turn on when a the ball save is enabled at the beginning of
a players turn. To avoid the timer running out (if the player takes a
break before plunging a ball) the timer can be configured not to start
until an event in this list is posted.

### console_log:

Single value, type: one of the following options: none, basic, full.
Default: `basic`

Log level for the console log for this device.

### debug:

Single value, type: `boolean` (`true`/`false`). Default: `false`

Set this to true to see more debug output.

### file_log:

Single value, type: one of the following options: none, basic, full.
Default: `basic`

Log level for the file log for this device.

### label:

Single value, type: `string`. Default: `%`

The plain-English name for this device that will show up in operator
menus and trouble reports.

### tags:

List of one (or more) values, each is a type: `string`. Defaults to
empty.

Special / reserved tags for ball saves: *None*

See the
[documentation on tags](instructions/tags.md) for details.

## Related How To guides

* [Ball Saves](../game_logic/ball_saves/index.md)
* [Ball Start and End Behavior](../game_logic/ball_start_end.md)
* [Kickbacks](../mechs/kickbacks.md)
