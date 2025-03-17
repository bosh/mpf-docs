---
title: How to create slides
---

!!! warning "MPF-MC is being deprecated"

    This instruction page is for the legacy MPF-MC for MPF versions 0.57 and prior. For users of MPF 0.80 and later, please refer to the [Godot Media Controller (GMC) Documentation](../../gmc/index.md)

# How to create slides


Since slides are so critical in MPF's display system, let's look at
how you actually create slides. You can test slides and widgets
interactively using
[Interactive MC (iMC)](../../tools/imc.md).

There are several ways you can define and create slides:

* In a [slides:](../../config/slides.md) section of a
    config file.
* Dynamically in the [slide_player:](../../config/slide_player.md) section of your config.
* Dynamically in a show config or show file.

Let's look at each of these options.

## Defining slides in the slides: section of a config file

The main way to do it is in the "slides" section of a config file,
like this:

``` yaml
slides:
  some_slide:
    - type: text
      text: THIS IS MY SLIDE
  some_other_slide:
    - type: text
      text: THIS IS ANOTHER SLIDE
    - type: text
      text: WITH MORE WORDS
      y: bottom
      anchor_y: bottom
  tilt_warning_1:
    - type: text
      text: WARNING
  tilt_warning_2:
    - type: text
      text: WARNING WARNING
```

In the example above, we have four main sub-entries in the slides
section:

* some_slide
* some_other_slide
* tilt_warning_1
* tilt_warning_2

Each of the above listed subsections represents a different slide, and
the names of those sections are used as the names of those slides. In
other words, this config has a slide called "some_slide", another
slide called "some_other_slide", etc.

You can list slides in a [slides:](../../config/slides.md)
section of either your machine-wide or a mode config. The most important
thing to know about slide names is that they are GLOBAL throughout MPF.
That means that MPF has a single master list of all the slide names used
in the entire game. (So don't use the same slide name twice or it will
get confused.)

The configuration entries under each slide name are the widgets that
will be added to that slide. (Each slide can have one or more widgets.
You can read about all the different types of widgets, as well as the
options for widget positioning and sizing, in the
[widgets](../widgets/index.md)
section of the documentation.

You'll probably end up creating hundreds of slides in your machine by
the time you're done with it.

!!! note

    The slides defined in the [slides:](../../config/slides.md)
    section are just the configurations that are used to create the slides
    when they're needed. In other words, no memory is used to "hold" the
    slides, so you can create lots and lots of them without worrying about
    running out of memory.

At this point, you're just creating the slides. Deciding when to show
which slide will come later.

Since MPF maintains a single global list of slides, it doesn't
technically matter whether you define your slides in the
[slides:](../../config/slides.md) section of your
machine-wide config or your mode config. Obviously though if you define
the slides a mode will use in that mode's config file, then that will
help you keep everything more organized.

## Dynamically defining slides in a slide_player: section of a config file

The [slide_player:](../../config/slide_player.md) section of a
machine-wide or mode config is where you tell MPF to show (or "play")
a specific slide when some event occurs. Full documentation for the
slide_player is in the [/config/slide_player](showing_slides.md)
section of the documentation.

You can define slides in the slide_player like this:

``` yaml
slide_player:
  some_event:
    my_slide_1:
      - type: text
        text: THIS IS MY SLIDE
##! test
#! post some_event
#! advance_time_and_run .1
#! assert_text_on_top_slide "THIS IS MY SLIDE"
```

In the above example, when the event *some_event* is posted, the slide
player will respond and show the slide called *my_slide_1* which will
include that single text widget.

It doesn't really matter whether you pre-define a slide in the
[slides:](../../config/slides.md) section of a config
versions dynamically defining it in the
[slide_player:](../../config/slide_player.md) section. Really it
comes down to personal preference. Some people like to have all their
slides in one location (all in the [slides:](../../config/slides.md) section), whereas others prefer to have the configuration
for the slides closer to where they will be used (by defining them in
the [slide_player:](../../config/slide_player.md) section). Most
people end up mixing-and-matching, with some quick-and-dirty one-time
use slides in the slide_player with other slides you might reuse in the
slides: section.

## Dynamically defining slides in a show config

As you'll learn in other parts of this documentation, anything that's
in one of the "_player" sections of the config (like the
"slide_player" above), can also be defined in a show configuration
(from a show file or a show configuration section of a config file).

So here's an example of a slide created within a show for use within a
specific step in that show:

``` yaml
#! show_player:
#!   start_show: my_show
##! show: my_show
# show_version=5
- time: 0
  slides:
    my_show_slide_1:
    - type: text
      text: MISSION PINBALL
      color: red
    - type: rectangle
      width: 128
      height: 32
##! test
#! post start_show
#! advance_time_and_run .1
#! assert_text_on_top_slide "MISSION PINBALL"
```

Again, see the [show documentation](../../shows/index.md) for details. Here we're just showing that it's also
possible to define a slide in a show config.
