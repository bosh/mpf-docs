---
title: "switch_player: Config Reference"
---

# switch_player: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `switch_player:` section of your config is where you can replay a
series of switches for testing purposes. Also have a look at the
[MPF monitor](../tools/monitor/index.md) for
interactive testing purposes.

This is an example:

``` yaml
#config_version=5
switches:
  s_test1:
    number:
    x: 0.4
    y: 0.5
    z: 0
  s_test2:
    number:
    x: 0.6
    y: 0.7
  s_test3:
    number:
plugins: switch_player
switch_player:
  start_event: test_start
  steps:
    - time: 100ms
      switch: s_test1
      action: activate
    - time: 600ms
      switch: s_test3
      action: hit
    - time: 100ms
      switch: s_test1
      action: deactivate
    - time: 1s
      switch: s_test2
      action: activate
    - time: 1s
      switch: s_test3
      action: hit
    - time: 100ms
      switch: s_test2
      action: deactivate
    - time: 1s
      switch: s_test3
      action: hit
```

## Optional settings

The following sections are optional in the `switch_player:` section of
your config. (If you don't include them, the default will be used).

### start_event:

Single event. The device will add an handler for this event. Default:
`machine_reset_phase_3`

Event to trigger the start of the switch player.

### steps:

Unknown type. See description below.

The steps of the switch_player. See the example above.

## Related Pages:

* [plugins:](plugins.md)
* [switch_player API Reference](../code/api_reference/core/switch_player.md)
