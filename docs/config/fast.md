---
title: "fast:"
---

# fast:


--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files|**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `fast:` section of your machine-wide config is where you configure
hardware options that are specific to the FAST Pinball Controller. Note
that we have a how to guide which includes
[all the FAST-specific settings](../hardware/fast/index.md) throughout your entire config file, so be sure to read that
if you have FAST hardware.

``` yaml
fast:
  ports: com3, com4, com5
```

## Required settings

The following sections are required in the `fast:` section of your
config:

### default_normal_debounce_close:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Defaults to empty.

Specifies the default value for the debounce time for switches that are
configured with `debounce: normal` when they close.

Even though this is listed as a required setting, this entry is in the
`mpfconfig.yaml` file, (with a value of `10ms`), so you don\'t have to
enter it here unless you want to override that.

Also, keep in mind that this setting is only a default. You can override
it for any switch in that switch\'s config.

### default_normal_debounce_open:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Defaults to empty.

Specifies the default value for the debounce time for switches that are
configured with `debounce: normal` when they open.

Even though this is listed as a required setting, this entry is in the
`mpfconfig.yaml` file, (with a value of `10ms`), so you don\'t have to
enter it here unless you want to override that.

Also, keep in mind that this setting is only a default. You can override
it for any switch in that switch\'s config.

### default_quick_debounce_close:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Defaults to empty.

Specifies the default value for the debounce time for switches that are
configured with `debounce: quick` when they close.

Even though this is listed as a required setting, this entry is in the
`mpfconfig.yaml` file, (with a value of `2ms`), so you don\'t have to
enter it here unless you want to override that.

Also, keep in mind that this setting is only a default. You can override
it for any switch in that switch\'s config.

### default_quick_debounce_open:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Defaults to empty.

Specifies the default value for the debounce time for switches that are
configured with `debounce: quick` when they open.

Even though this is listed as a required setting, this entry is in the
`mpfconfig.yaml` file, (with a value of `2ms`), so you don\'t have to
enter it here unless you want to override that.

Also, keep in mind that this setting is only a default. You can override
it for any switch in that switch\'s config.

### ports:

List of one (or more) values, each is a type: `string`. Defaults to
empty.

A comma-separated list of the serial port names your FAST controller
uses.

## Optional settings

The following sections are optional in the `fast:` section of your
config. (If you don\'t include them, the default will be used).

### baud:

Single value, type: `integer`. Default: `921600`

The baud rate for the FAST COM ports.

### console_log:

Single value, type: one of the following options: none, basic, full.
Default: `none`

Log level for the console log for this platform.

### debug:

Single value, type: `boolean` (`true`/`false`). Default: `false`

See the [documentation on the debug setting](instructions/debug.md) for details.

### dmd_buffer:

Single value, type: `integer`. Default: `3`

Max backlog for the DMD port to prevent overflows in the FAST CPU.

### driverboards:

Single value, type: one of the following options: fast, wpc, None.
Defaults to empty.

Which driverboards are you using? Most likely `fast`. Similar to
`driverboards` in the [hardware:](hardware.md)
section. Use this setting if you use multiple playforms (i.e. FAST and
P3-Roc) in one machine.

### file_log:

Single value, type: one of the following options: none, basic, full.
Default: `basic`

Log level for the file log for this platform.

### hardware_led_fade_time:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Default: `0`

Controls how quickly LEDs will fade to their new color when they receive
a color instruction from MPF.

The default is 0, which means if you set an LED to be red, it will turn
red instantly. But if you set `hardware_led_fade_time: 20`, that means
that when an LED receives an instruction to turn RED, it will smoothly
fade from whatever color it is now to red over a period of 20ms.

You can play with different settings to pick something you like. Some
people prefer the instant 0ms snappiness that\'s possible with LEDs.
Others like to set this value to something like `100ms` which gives LEDs
the more gentle fade style reminiscent of incandescent bulbs.

### ignore_rgb_crash:

Single value, type: `boolean` (`true`/`false`). Default: `false`

Ignore if the RGB CPU crashes. It will restart and the light will mostly
recovery within a few seconds. If you set this to `False` MPF will
shutdown when this happens because the hardware state is undefined when
this happens.

### net_buffer:

Single value, type: `integer`. Default: `10`

Max backlog for the NET port to prevent overflows in the FAST CPU.

### rgb_buffer:

Single value, type: `integer`. Default: `3`

Max backlog for the RGB port to prevent overflows in the FAST CPU.

### watchdog:

Single value, type: `time string (ms)`
[Instructions for entering time strings](instructions/time_strings.md). Default: `1000`

The FAST controllers include a "watchdog" timer. A watchdog is a timer
that is continuously counting down towards zero, and if it ever hits
zero, the controller shuts off all the power to the drivers. The idea is
that every time MPF runs a game loop (so, 30 times a second or
whatever), MPF tells the FAST controller to reset the watchdog timer. So
this timer is constantly getting reset and never hits zero.

But if MPF crashes or loses communication with the FAST controller, then
this watchdog timer won\'t be reset. When it hits zero, the FAST
controller will kill the power to the drivers. This should prevent an
MPF crash from burning up driver or somehow damaging your hardware in
another way.

You can set the watchdog timer to whatever you want. (This is
essentially the max time a driver could be stuck "on" if MPF crashes.)
The default is 1 second which is probably fine for almost everyone, and
you don\'t have to include this section in your config if you want to
use the default.

## Related How To guides

* [How to configure MPF for FAST Pinball hardware](../hardware/fast/index.md)
