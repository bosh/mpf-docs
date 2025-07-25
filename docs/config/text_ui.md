---
title: "text_ui: Config Reference"
---

# text_ui: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `text_ui:` section of your config is where you configure the Text UI
that appears in the console while MPF is running.

The Text UI displays information about the machine and game: switch
states, active modes, variable values, and game information. By default,
it displays all machine variables and player variables.

Depending on the complexity of your game and the mode you're working
on, you may not want the Text UI to display *every* variable. In that
case, you can use the `text_ui:` section to specify which player and
machine variables you want to see.

## Optional settings

The following sections are optional in the `text_ui:` section of your
config. (If you don't include them, the default will be used).

### machine_vars:

List of one (or more) values, each is a type: `string`. Defaults to
empty.

A list of all of the machine variables to display and update in the Text
UI. If the list is empty, no machine variables will be displayed.

If the `machine_vars:` setting is not included in your config, *all*
machine variables will be displayed.

### player_vars:

List of one (or more) values, each is a type: `string`. Defaults to
empty.

A list of all of the player variables to display and update in the Text
UI.

While a game is active, MPF will always show three player variables:
player number, ball number, and player score. If the `player_vars:`
setting is provided, the variable names listed will also be shown in the
Text UI.

If the `player_vars:` setting is not included in your config, *all*
player variables will be displayed.

## Related Pages:

* [Dynamically Updating Text](../mc/widgets/text/text_dynamic.md)
* [text_ui API Reference](../code/api_reference/core/text_ui.md)