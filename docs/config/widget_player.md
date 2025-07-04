---
title: "widget_player: Config Reference"
---

# widget_player: Config Reference

--8<-- "config_section.md"

| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**YES** :white_check_mark:|

!!! note

    This section can also be used in a show file in the `widgets:` section
    of a step.

The `widget_player:` section of your config is where you configure
widgets to be added to, removed from, or updated on slides (or parent
frames) based on events being posted.

This is an example:

``` yaml
#! widgets:
#!   widget_1: []
widget_player:
  some_event:
    widget_1:
      slide: slide_2
```

It will add widget_1 to slide_2.

See [Widget player](../config_players/widget_player.md) for
details.

## Settings

The following sections can be added under the the a particular widget's
settings `widget_player:` section of your config. (If you don't include
any of them, the default will be used).

So again, the format in a config file would be:

``` yaml
#config_version=5

widget_player:
   some_event:
      name_of_your_widget:
         <list of settings below go here>
   some_other_event:
      name_of_a_different_widget:
         <list of settings below go here>
```

And the format in a show file would be:

``` yaml
#show_version=5

- duration: 1s
  widgets:
      name_of_your_widget:
         <list of settings below go here>
      name_of_a_different_widget:
         <list of settings below go here>
```

Here are the settings you can use:

## Optional settings

The following sections are optional in the `widget_player:` section of
your config. (If you don't include them, the default will be used).

### action:

Single value, type: one of the following options: add, remove, update.
Default: `add`

#### `add`:

The widget or widget group is added to the slide or display target.

#### `remove`:

The widget or widget group is removed from the slide or display target.

#### `update`:

One or more of the widget or widget group's properties is updated.

### key:

Single value, type: `string`. Defaults to empty.

Used to uniquely identify a widget. With "add" actions, this sets the
key name, and with "remove" or "update" actions, the key is used to
identify which widget should be removed or updated.

Note that more than one widget (across displays and across slides) can
have the same key, and if you remove a widget based on a key, it will
remove all the widgets with that key. (In fact this is how MPF works
internally to remove all widgets that were created by a mode when that
mode ends.)

See the [Widget Keys](../mc/widgets/keys.md) guide for
details.

### slide:

Single value, type: `string`. Defaults to empty.

The name of the slide you want to add this widget to. If this is not
specified, then the widget will be added to whichever slide is currently
active on the default display.

### target:

Single value, type: `string`. Defaults to empty.

The name of the display or slide frame this widget will be added to.
When this setting is used, the widget is not added to a slide, rather,
it's added "on top" of the slide (to the parent display or slide
frame). See the [Widget layers, z-order, & parent frames](../mc/widgets/layers.md)
guide for details.

Note that the `target:` and `slide:` setting are fundamentally not
compatible with each other. If you used both, the `target:` setting will
be used and the `slide:` value will be ignored.

### widget_settings:

Unknown type. See description below.

``` yaml
widget_player:
  trigger_event:
    my_widget:
      widget_settings:
        z: 1
```

## Related How To guides

* [Widget player](../config_players/widget_player.md)
