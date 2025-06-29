---
title: "sound_system: Config Reference"
---

# sound_system: Config Reference

--8<-- "config_section.md"

!!! warning "Deprecated in MPF 0.80

    The GMC used by MPF 0.80 manages audio outside of YAML configuration files. See the [GMC Setup Guide](../gmc/setup.md) for instructions on configuring the audio system in GMC. The `sound_system:` configuration should be removed for projects upgrading to MPF 0.80 and Godot.


| Valid in | |
|-----|:----:|
|[machine](instructions/machine_config.md) config files |**YES** :white_check_mark:|
|[mode](instructions/mode_config.md) config files|**NO** :no_entry_sign:|

The `sound_system:` section of your machine config controls the general
settings for the machine's sound system. (This section is part of the
MPF media controller and only available if you're using MPF-MC for your
media controller.)

Here's an example of a typical sound configuration.

``` yaml
machine_vars:
  master_volume:
    initial_value: 0.8

sound_system:
  buffer: 1024
  channels: 1
  enabled: true
  frequency: 44100
  tracks:
    music:
      type: standard
      simultaneous_sounds: 1
      volume: 0.5
    voice:
      type: standard
      simultaneous_sounds: 1
      volume: 0.7
    sfx:
      type: standard
      simultaneous_sounds: 8
      volume: 0.4
```

## Required settings

The following sections are required in the `sound_system:` section of
your config:

### tracks:

One or more sub-entries. Each in the format of `string` :
[sound_system_tracks:](sound_system_tracks.md)

Every sound that's played in MPF is played on a track. If you are
familiar with an audio mixer a track can be thought of as a mixer
channel. Each track can have it's own settings, and you can set volume
on a per-track basis. You can have up to 8 audio tracks in your MPF
machine. The example above shows three tracks, called *music*, *voice*,
and *sfx*. The idea (in case it isn't obvious) is that you play all
your music clips on the music track, voice callouts on the voice track,
and the sound effects on the sfx track. To create a track, add a sub
entry to the `tracks:` section which will be the name of
that track. (So again, `music:`, `voice:` and
`sfx:` in the example.)

## Optional settings

The following sections are optional in the `sound_system:` section of
your config. (If you don't include them, the default will be used).

### buffer:

Single value, type: `integer`. Default: `2048`

This is the size of your sound buffer. It must be a power of 2. The
exact value you should use may take some trial-and-error. A bigger
buffer means that there's less chance of skipping and dropout (lower
CPU usage), but it also means that sounds can take longer to play since
the buffer has to fill first. Some limited power platform have to run
with a buffer of 4096 or 8192 or 16384, others at 512 or 256. So just
play with it and see what works for you.

### channels:

Single value, type: `integer`. Default: `1`

The number of channels the sound system will support. 1 for mono, 2 for
stereo. You're probably thinking, "aww man, I need stereo sound!" But
almost no pinball machines do this since the speakers in the backbox are
2 feet apart and they're 4 feet away from the player's ears. (Maybe if
you're going to use headphones or put tweeters in the front of the
machine?) Again, if you have a resource-constrained system, then go for
mono and make sure all your sound files are mono. If not, meh, go ahead
and use stereo.

### enabled:

Single value, type: `boolean` (`true`/`false`). Default: `true`

Indicates whether or not the sound system will be enabled in your
machine.

### frequency:

Single value, type: `integer`. Default: `44100`

How many sound samples per second you want. 44100 is so-called "CD
quality" audio, though with the sound systems in most pinball machines,
if you cut it in half (to 22050) it still sounds virtually the same. If
you're running on a resource-constrained host computer, you should make
sure all your sound files are encoded at the same rate so MPF doesn't
waste time re-encoding them on the fly. Smaller values mean smaller
sound files, less memory consumption, and less CPU processing. So if
you're on a resource constrained host computer, think about 22050
instead of 44100. (But be sure to resample all your sound files to
match.)

### master_volume:

Unknown type. See description below.

DEPRECATED! Will removed in future MPF versions.

Master volume has been moved to the machine variable `master_volume`.
You can use the following snippet:

``` yaml
machine_vars:
  master_volume:
    initial_value: 0.8
```

Note that this only controls the volume of the MPF app, not the host
OS'es system volume. So you still need to make sure that the host OS is
not on mute and that the volume is turned up.

## Related How To guides

* [Sounds, Music & Audio](../mc/sound/index.md)
