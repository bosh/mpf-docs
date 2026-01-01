---
title: "fast:exp: Config Reference"
---

# fast:exp: Config Reference

--8<-- "deeper_config_section.md"

| Valid in | |
|-----|:----:|
|[machine](../instructions/machine_config.md) config files|**YES** :white_check_mark:|
|[mode](../instructions/mode_config.md) config files|**NO** :no_entry_sign:|

Within the [`fast:exp:`](fast_exp.md) (or `fast_int:`) section of your [fast:](../fast.md) config, you configure each of your EXP boards, as well as your Neuron EXP interface.

### model:

The product number of the IO board. E.G. `FP-EXP-0081`
Supported models in MPF as of 0.57.4 are:

* `FP-EXP-2000` - the Neuron LED ports
* `FP-EXP-1313` - single shaker interface
* `FP-EXP-0051` - DC motors, 4 LED headers
* `FP-EXP-0061` - Stepper board, 4 LED headers
* `FP-EXP-0071` - Servo board, 4 LED headers
* `FP-EXP-0081` - 2x4 LED headers
* `FP-EXP-0091` - 4 LED headers, custom breakouts

### address:

Single value, string, default: special

If you do not provide a value, the default address for the board model will be used.
If you are using only a single board of each model number, it should not be necessary to specify the address.
If you are using multiple boards with the same model, you must disambiguate them by modifying the solder jumpers.
The guide for this is in the [FAST website documentation](https://fastpinball.com/programming/exp/#expansion-board-addresses), but the general algorithm is that Jumper 0 being closed will increment the address by 1,
and Jumper 1 will increment the address by 2, so both will increment by 3, allowing up to four boards of the same model to be used in one system.

The FAST website has the full chart, but these are the default address values for each supported EXP board:

* `FP-EXP-2000` - `48`
* `FP-EXP-1313` - `30`
* `FP-EXP-0051` - `D0`
* `FP-EXP-0061` - `90`
* `FP-EXP-0071` - `B4`
* `FP-EXP-0081` - `84`
* `FP-EXP-0091` - `88`

### breakouts:

Single value, type: `dict`. Defaults to empty.

Specification of the remote breakout boards.

### led_ports:

Single value, type: `dict`. Defaults to empty.

Specification of the LED port configurations.

### led_fade_time:

Single value, type: `time string (ms)`
[Instructions for entering time strings](../instructions/time_strings.md). Defaults to empty.

Specify the fade default for LEDs on this board. Note that values above the limit of 8191 (ms) will raise an error.

### led_hz:

Single value, float, default: `30`

Specify the refresh rate of the LEDs for this board. Note that values above the limit of 31.25 will be set to the limit.

### ignore_led_errors:

Single value, boolean, default: `false`

If false, LED hex communication decode errors will be raised as errors when encountered from this board.
If you encounter instability due to these errors, set this to true to silently ignore them.

## Using `exp_int:` - Raspberry Pi only

If using a Raspberry Pi connected directly to the Neuron controller, the LED headers on the Neuron will not be available on the normal EXP interface.
In order to access these Neuron LED headers, you must define a parallel structure to the existing `exp:` configuration, and move the Neuron EXP board definition over to it.

Example:

```yaml
fast:
  exp_int:
    boards:
      neuron:
        model: FP-EXP-2000
```

## FAST Docs:

For more information, see the [FAST EXP Interface MPF Config page](https://fastpinball.com/mpf/config/exp/).
