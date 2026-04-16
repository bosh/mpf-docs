---
title: "Tutorial 5: Add a display"
---

# Tutorial 5: Set up Godot, GMC, and your first Scene

--8<-- "tutorial.md"

In this step, we're going to set up the Godot Media Controller,
a plugin used by Godot to integrate that game engine with MPF.
The MPF game engine is generally in control of what Godot and GMC
do, but Godot is a full multimedia game engine on its own, so
advanced features like animation, video modes and minigames are
possible if you want to get creative. MPF's built-in modes like
tilt, high score, and bonus, as well as the service menu are all
implemented via GMC and Godot.

## 1. The media controller and how it works

Remember from the MPF [MPF overview: How MPF works](../../start/index.md)
section (you read that, right?) that MPF is actually two separate
pieces--the *game engine* and the *media controller*.

Up until this point in the tutorial, we've been running the MPF game
engine only. In this step, we'll be switching for the first time to the
media controller.

## 2. Your tutorial is in another castle

MPF with Godot has its own tutorial in another section of this site.
Go there. Here. You'll set up the Godot editor, the GMC plugin,
an attract slide, and maybe some sounds. See you soon!

## 3. Running your game with and without Godot enabled

Depending on the situation and which features you are testing, you may
want to run with GMC either enabled or disabled. It is easy to swap between
these options, the command line flag `-b` controls this.

If you run `mpf game` without `-b`, the game process will stop early on
in setup and wait to connect to a GMC instance using the BCP protocol
by default on port 5050. If you are doing something like debugging hardware
or configuring devices in monitor, you might want to skip the Godot
part entirely. In that case, add `-b`, so `mpf game -b`, and then MPF
will completely skip the GMC communication. This means that if you
run GMC anyway, MPF will NOT connect to it and it will be stuck at the
start screen.

## 4. Running your game and GMC in the same command

There are three ways that you can set up your game to start up.
The first is what you saw in the GMC tutorial - run MPF and then run GMC
separately.

The alternatives are to configure the Godot project to automatically start
the MPF engine when it starts, or to do the opposite and have the MPF start
command also start the Godot game.

Either option is fine - the choice really depends on your workflow, which
may change over time and you develop and finish your game.

The Godot option is discussed in another guide. TODO
For this tutorial, because we already frequently run `mpf` on the command line,
we will set up the command line-based start command, `mpf both`.

This command works the same as the `mpf game` command that we usually run
(the same as `mpf` with no game option, as `game` is the default if unspecified),
and this means you can use the options `-t`, `-x`, etc. as you already have been.

The difference is that `mpf both` automatically starts the Godot project of
the folder it is executed in. Since `mpf game` is run in your game folder,
`mpf both` is the same, and it looks for a `project.godot` in that folder.

If you opted to configure your Godot project (and project.godot) file elsewhere,
you will need to pass the option `-g <path-to-project-folder>`, like `mpf both -g my_gmc_subfolder`.

The `both` command also needs to be able to find the Godot executable, either on the
$PATH as `godot` or via the command line option `-G <path-to-godot-v4.x.exe>`.

All together this means you may run a command looking something like `mpf both -t -g gmc_subfolder -G /bin/godot_installs/godot.exe`. The one option you never want with `both` is `-b`, because that will tell the MPF game engine to ignore Godot
while simultaneously starting the Godot engine anyway!

## 5. Run it already!

If you have `mpf both` working, you should see the MPF engine in the command line while
a Godot window showing the MPF startup screen pops up. If both engines start up
successfully, they should connect to one another and advance into the "attract" mode.

Lots of things can make either engine fail to start up, and usually the other one will
be completely stuck when this happens. Syntax errors, configuration mistakes, leftover
MPF or Godot processes from previous runs - these can all cause one or both sides to have issues.
The key is to test changes frequently and read logs thoroughly, and if all else fails,
ask for help (but first grab your logs, since that's the only way helpers can understand what is going on!)

## 4. Adding a slide and a slide_player config

Now we're going create a slide and play it (TODO for some reason!)

This kind of work requires changing both MPF YAML configuration as well as using the Godot
editor to create Godot resources.

Create a slide in Godot somehow

Now back into MPF YAML, create a new section in your config (WHERE) called `slide_player:`.
The `slide_player` watches for certain events to occur, and when they do, it
"plays" or takes action on a slide.

To see this working, add the following section to your machine config:

``` yaml
slide_player:
  init_done: welcome_slide
```

What this is doing is saying, "When the event called *init_done* #TODO IS THIS USED?
happens, play the slide called *welcome_slide*." The *init_done* is an
event that's posted by MPF-GMC at the earliest possible point when it is
ready after it initially starts up (literally it's saying "the GMC is
ready"). So what we're doing here is having MPF listen for the GMC ready event, and
then having MPF tell MPF-GMC to show our welcome slide.
(Check out the [events](../../events/index.md) documentation for details on what events are.)

To verify, run `mpf both` again, and hopefully you see something like this:

![image](images/mc_pinball_1.png) #TODO DEF DIFFERENT

By the way, if you're wondering how we know what events to use,
there's an [event reference](../../events/index.md) in the documentation which has a list of all the events in
MPF as well as descriptions of when they're posted. You can use any of
these as triggers for your slides via the `slide_player:`.

To quit MPF, just make sure the graphical window has focus and hit the
`Esc` key. That should cause both the MPF game engine and GMC to exit.

## Check out the complete config.yaml file so far

If you want to see a complete `config.yaml` file up to this point, it's
available in the "tutorials" folder of the mpf-examples package that
you should have downloaded in Step 1 of this tutorial.

There are config files for each step, so the config for Step 5 should be
at `/mpf-examples/tutorial/step_5`.
