---
title: Working with Displays
---

!!! warning "MPF-MC is being deprecated"

    This instruction page is for the legacy MPF-MC for MPF versions 0.57 and prior. For users of MPF 0.80 and later, please refer to the [Godot Media Controller (GMC) Documentation](../../gmc/index.md)

# Working with Displays


The first step to setting up a display in MPF is to use the `displays:`
section of your machine-wide config to create a list of displays.

Note that the Tutorial includes a walk-through of setting up your first
display. So if you just want to get it up and running quickly, check out
the [tutorial](../../tutorial/index.md) instead
and then come back here for the nitty-gritty details later.

Here's a very simple example that creates a display called "window"
with a height and width of 800x600:

``` yaml
displays:
  window:
    width: 800
    height: 600
```

You can name your display whatever you want. For example, here's a
display called "potato" which is 100x100:

``` yaml
displays:
  potato:
    width: 100
    height: 100
```

You can add multiple displays to your config. Here's an example with a
display called "lcd" which is 1366x768, and a second display called
"playfield" which is 640x480:

``` yaml
displays:
  lcd:
    width: 1366
    height: 768
    default: true
  playfield:
    width: 640
    height: 480
```

The "lcd" display above also has a setting `default: true`. As you can
imagine, when you have more than one display, then when you are setting
up content to be shown on the display, you have to specify which display
you want it to show up on. Picking one display to be your default is the
display that's used for content where you don't explicitly set which
display you're using.

!!! note

    Full details and options for these displays are available in the
    [displays: section](../../config/displays.md) of
    the config file reference.

**These "displays" are logical, not physical!**

One concept that's somewhat confusing for new users is that the
displays you set up here are not yet tied to physical displays in your
pinball machine. You can think of these as "logical" displays which
you can use in your config files and game code. But when it comes to
using a physical display, you have to "link" the physical display
hardware to one of these logical displays.

One final note about the displays you specify in your `displays:`
section: The size (height and width) of your displays here are
independent from the actual physical displays (windows and DMDs). For
example, the size of the on-screen window is specified in the `window:`
section of the machine config (which is 800x600 by default). So if you
change the size of your display here (perhaps to 320x240), then the
on-screen window will still be 800x600, and the content of the display
canvas will be 320x240 (but scaled up to the 800x600 window). This means
that MPF is "resolution independent", in that you can build your game
for a certain display size and then scale it up or down to fit on
whatever physical display is there later.

Let's walk through some examples of how you can actually configure
various displays. You don't have to read through all of these---just
pick whichever display type you want to use in your machine.

* [lcd](lcd.md)
* [dmd](dmd.md)
* [rgb_dmd](rgb_dmd.md)
* [adding_dot_look_to_lcd](adding_dot_look_to_lcd.md)
* [alpha_numeric](alpha_numeric.md)
* [multiple_screens](multiple_screens.md)

**A note on performance with "displays" and "dmds"**

If you have a physical DMD defined (in the
[dmds:](../../config/dmds.md) or
[rgb_dmds:](../../config/rgb_dmds.md) of your
machine config) and are emulating the DMD's slides and widgets in your
window, be aware that the MPF media controller will process the graphics
data for the physical DMD *even when MPF is running in "virtual"
mode*.

Although that graphics data will not be sent to a physical DMD,
processing it provides a more realistic MPF experience because of the
considerable CPU power required to convert on-screen graphics to DMD
data.

If you are planning to use a physical DMD at some point on your project,
it's recommended to configure one *before* you start designing your
slides and widgets. Especially if you will be running virtually for the
bulk of your early game design: you don't want to spend time designing
intricate slides and high-resolution graphics only to find your CPU
crumble when you finally attach a physical DMD.
