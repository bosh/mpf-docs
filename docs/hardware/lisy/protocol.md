---
title: LISY Protocol
---

# LISY Protocol


The LISY protocol is a generic serial protocol to control pinball
machines. It was developed for the
[LISY platform](../../index.md) but is also used
in other custom pinball platforms such as
[APC](../apc/index.md).

## Theory of operations

All communication is initiated from the host PC. Commands are binary and
generally have a fixed length. They may contain a length byte to
indicate how many entries are to expect (i.e. three color values).
Strings are zero terminated in both command and response.

At startup MPF resets the hardware and queries the count of all
peripherals (i.e. switches, coils, lamps). Afterwards, it will query the
state of switches and configure coils/lamps.

During the runtime MPF periodically polls changed switches and sends a
watchdog every 500ms. The platform is expected to disable all outputs
after 1s without watchdog.

## Limitations

Let us know if you hit any of those and we can develop a plan forward.

* Max 127 switches are supported (because polling uses the upper bit
    as state)
* Max 256 hardware sounds (alternatively, you can use the MPF sound
    system)
* Max 256 simple lamps (on/off only)
* Max 256 lights (with fading and brightness)
* Max 256 coils (MPF currently only supports 100 of them; let us
    know if you need more)
* Max 7 alphanumeric displays (BCD, 7-segment or 14-segment
    displays)
* No error correction on the wire (your serial should be reliable)

## Protocol reference (v0.08)-

```
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Command Byte (see table below

  1 - n          n - 1          Payload (n-1 bytes)-

  : General command format

  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0 to (n-1)     n - 1          String

  n              1              Null byte
  ------------------------------------------------------------------------
```

  : String format (in both payload and response)

### Get Connected Hardware (0x00)

Get the name of the connected hardware. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Command 0 - Get Connected Hardware

  -----------------------------------------------------------------------
```

  : Command 0x00 - Get Connected Hardware

Returns a null terminated string.

Example: `LISY80`.

MPF uses this string to identify the platform and might perform certain
quirks based on this info if necessary. Currently, there is quirk
`coils_start_at_one` for `LISY1`, `LISY35` and `APC` to index coils
starting at one instead of zero.

### Get Firmware Version (0x01)

Get firmware version of the hardware board. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           1           Command 1 - Get Firmware Version

  -----------------------------------------------------------------------
```

  : Example Command 0x01 - Get Firmware Version

Returns a null terminated string.

Example: `4.01`.

MPF parses this string as semantic version. It exposes the version as
variable and in the logs. This might be used to perform quirks around
known bugs.

### Get API Version (0x02)

Get the API version. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           2           Command 2 - Get API Version

  -----------------------------------------------------------------------
```

  : Example Command 0x02 - Get API Version

Returns a null terminated string.

Example: `0.08`.

MPF parses this string as semantic version. This is expected to be
`0.08` for this version. MPF might refuse old API versions at some
point.

### Get Simple Lamp Count (0x03)

Get count of lamps connected to the hardware platform. Does not have any
payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           3           Command 3 - Get Simple Lamp Count

  -----------------------------------------------------------------------
```

  : Example Command 0x03 - Get Simple Lamp Count

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Simple Lamp count `l` (0 to 255). 0 if no
                                simple lamps exist.

  ------------------------------------------------------------------------
```

  : Response to 0x03 - Get Simple Lamp Count

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           64          Platform supports 64 simple lamps
                                      with numbers 0 to 63.

  -----------------------------------------------------------------------
```

  : Example Response to 0x03 - Get Simple Lamp Count

MPF uses this number to refuse any lights with a number larger or equal
than `l` and subtype `lamp`. Lamps in LISY are expected to be `on/off`
type devices and do not support fading or dimming. Use this for older
style lamps and GIs.

### Get Solenoid Count (0x04)

Get count of solenoids connected to the hardware platform. Does not have
any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           4           Command 4 - Get Solenoid Count

  -----------------------------------------------------------------------
```

  : Example Command 0x04 - Get Solenoid Count

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Solenoid count `c` (0 to 127). 0 if no
                                solenoids exist.

  ------------------------------------------------------------------------
```

  : Response to 0x04 - Get Solenoid Count

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           64          Platform supports 64 solenoids with
                                      numbers 0 to 63.

  -----------------------------------------------------------------------
```

  : Example Response to 0x04 - Get Solenoid Count

MPF uses this number to refuse any solenoids with a number larger or
equal than `c`.

### Get Sound Count (0x05)

Get count of sounds available. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           5           Command 5 - Get Sound Count

  -----------------------------------------------------------------------
```

  : Example Command 0x05 - Get Sound Count

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Sound count `o` (0 to 255). 0 if no sounds
                                exist.

  ------------------------------------------------------------------------
```

  : Response to 0x05 - Get Sound Count

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           128         Platform supports 128 sounds with
                                      numbers 0 to 127.

  -----------------------------------------------------------------------
```

  : Example Response to 0x05 - Get Sound Count

MPF uses this number to refuse any sounds with a number larger or equal
than `o`. This is used for older machines with a hardware soundcard. In
[LISY](../../index.md) it can be used to play
sounds from the ROM of the original game. Return `0` if you do not
support sounds in your platform.

### Get Segment Display Count (0x06)

Get count of segment displays available. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           6           Command 6 - Get Segment Display
                                      Count

  -----------------------------------------------------------------------
```

  : Example Command 0x06 - Get Segment Display Count

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Segment display count `sd` (0 to 255). 0
                                if no sounds exist.

  ------------------------------------------------------------------------
```

  : Response to 0x06 - Get Segment Display Count

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           6           Platform supports 6 segment
                                      displays with numbers 0 to 5.

  -----------------------------------------------------------------------
```

  : Example Response to 0x06 - Get Segment Display Count

MPF uses this number to refuse any segment display with a number larger
or equal than `sd`. Return `0` if you do not support displays in your
platform.

### Get Segment Display Details (0x07)

Get type of segment displays.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `sd` of the segment display to query

  ------------------------------------------------------------------------
```

  : Payload of Command 0x07 - Get Segment Display Details

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           7           Command 7 - Get Segment Display
                                      Details

  1           1           0           Query the first display
  -----------------------------------------------------------------------
```

  : Example Command 0x07 - Get Segment Display Details

Returns two bytes:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Type of segment display (see list below)

  1              1              Number of segments `sw(sd)` (0-255)-
```

  : Response to 0x07 - Get Segment Display Details

`sw(sd)` is the segment width for display index `sd`.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           1           Segment display is a BCD7 display

  1           1           12          Segment display is 12 segments wide
  -----------------------------------------------------------------------
```

  : Example Response to 0x07 - Get Segment Display Details

Options are:

```
+-----------+-----------+-----------+-----------------------------------+
| Byte of   | Name      | De        | Bytes per Segment `bs(st)`        |
| segment   |           | scription |                                   |
| type `st` |           |           |                                   |
+===========+===========+===========+===================================+
| 0         | Invalid   | Display   | *                               |
|           |           | index is  |                                   |
|           |           | invalid   |                                   |
|           |           | or does   |                                   |
|           |           | not exist |                                   |
|           |           | in        |                                   |
|           |           | machine.  |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 1         | BCD7      | BCD Code  | 1 byte (4 bit BCD in the first    |
|           |           | for 7     | four byte)                        |
|           |           | Segment   |                                   |
|           |           | Displays  |                                   |
|           |           | without   |                                   |
|           |           | comma     |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 2         | BCD8      | BCD Code  | 1 byte (4 bit BCD in the first    |
|           |           | for 8     | four byte, 7th byte is the comma) |
|           |           | Segment   |                                   |
|           |           | Displays  |                                   |
|           |           | (same as  |                                   |
|           |           | BCD7 but  |                                   |
|           |           | with      |                                   |
|           |           | comma)    |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 3         | SEG7      | Fully     | 1 byte (a-g encoded as bit 0 to 6 |
|           |           | ad        | and bit 7 as comma)               |
|           |           | dressable |                                   |
|           |           | 7 Segment |                                   |
|           |           | Display   |                                   |
|           |           | (with     |                                   |
|           |           | comma)    |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 4         | SEG14     | Fully     | 2 bytes (a-g encoded as bit 0 to  |
|           |           | ad        | 6 in first byte. h to r encoded   |
|           |           | dressable | as bit 0 to 6 in second byte.     |
|           |           | 14        | comma as bit 7 in second byte)    |
|           |           | Segment   |                                   |
|           |           | Display   |                                   |
|           |           | (with     |                                   |
|           |           | comma)    |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 5         | ASCII     | ASCII     | 1 ascii byte per segment          |
|           |           | Code      |                                   |
+-----------+-----------+-----------+-----------------------------------+
| 6         | ASCII_DOT | ASCII     | 1 ascii byte per segment.         |
|           |           | Code with | Additionally bit 7 encodes the    |
|           |           | comma     | comma.                            |
|           |           | (every    |                                   |
|           |           | segment   |                                   |
|           |           | has an    |                                   |
|           |           | a         |                                   |
|           |           | dditional |                                   |
|           |           | comma)    |                                   |
+-----------+-----------+-----------+-----------------------------------+
```

: Types in Response of 0x07 - Get Segment Display Details

Not yet used in MPF but will be added soon.

### Get Game Info (0x08)

Get the game number. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           8           Command 8 - Get Game Info

  -----------------------------------------------------------------------
```

  : Example Command 0x08 - Get Game Info

Returns null terminated string. This is the internal Gottlieb number in
LISY. MPF does not use the command at all (and we are not planning to).
It is used in PinMAME on LISY.

### Get Switch Count (0x09)

Get count of switches available. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           9           Command 9 - Get Switch Count

  -----------------------------------------------------------------------
```

  : Example Command 0x09 - Get Switch Count

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Switch count `s` (0 to 127)-
```

  : Response to 0x09 - Get Switch Count

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           70          Platform supports 70 switches with
                                      numbers 0 to 69.

  -----------------------------------------------------------------------
```

  : Example Response to 0x09 - Get Switch Count

MPF uses this number to refuse any switches with a number larger or
equal than `s`. Please note that the procotol is currently limited to
127 switches since the upper byte is used to indicate inverted switches
in commands.

### Get Status of Simple Lamp (0x0A)

Get the status of a simple lamp. Payload is the lamp index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `l` of the lamp to query

  ------------------------------------------------------------------------
```

  : Payload of Command 0x0A - Get Status of Simple Lamp

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           10          Command 10 - Get Status of Simple
                                      Lamp

  1           1           25          Query status of lamp 25
  -----------------------------------------------------------------------
```

  : Example Command 0x0A - Get Status of Simple Lamp

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              0=Off, 1=On, 2=Lamp not existing

  ------------------------------------------------------------------------
```

  : Response to 0x0A - Get Status of Simple Lamp

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Status of lamp is off

  -----------------------------------------------------------------------
```

  : Example Response to 0x0A - Get Status of Simple Lamp

MPF will not use this. After init/reset MPF assumes all lights to be in
state off.

### Set Status of Simple Lamp to On (0x0B)

Set simple lamp to on. Payload is the lamp index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `l` of the lamp to set to on

  ------------------------------------------------------------------------
```

  : Payload of Command 0x0B - Set Status of Simple Lamp to On

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           11          Command 11 - Set Status of Simple
                                      Lamp to On

  1           1           25          Set lamp 25 to on
  -----------------------------------------------------------------------
```

  : Example Command 0x0B - Set Status of Simple Lamp to On

No response is expected.

### Set Status of Simple Lamp to Off (0x0C)

Set simple lamp to off. Payload is the lamp index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `l` of the lamp to set to off

  ------------------------------------------------------------------------
```

  : Payload of Command 0x0C - Set Status of Simple Lamp to Off

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           12          Command 12 - Set Status of Simple
                                      Lamp to Off

  1           1           25          Set lamp 25 to off
  -----------------------------------------------------------------------
```

  : Example Command 0x0C - Set Status of Simple Lamp to Off

No response is expected.

### Get Status of Solenoid (0x14)

Get the status of a solenoid. Payload is the solenoid index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to query

  ------------------------------------------------------------------------
```

  : Payload of Command 0x14 - Get Status of Solenoid

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           20          Command 20 - Get Status of Solenoid

  1           1           25          Query status of solenoid 25
  -----------------------------------------------------------------------
```

  : Example Command 0x14 - Get Status of Solenoid

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              0=Off, 1=On, 2=Solenoid not existing

  ------------------------------------------------------------------------
```

  : Response to 0x14 - Get Status of Solenoid

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Status of solenoid is off

  -----------------------------------------------------------------------
```

  : Example Response to 0x14 - Get Status of Solenoid

MPF will not use this. After init/reset MPF assumes all solenoids to be
in state disabled.

### Enable Solenoid at Full Power (0x15)

Enable solenoid at full power. Payload is the solenoid index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to enable

  ------------------------------------------------------------------------
```

  : Payload of Command 0x15 - Enable Solenoid at Full Power

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           21          Command 21 - Enable Solenoid at
                                      Full Power

  1           1           25          Enable solenoid 25 at full power
  -----------------------------------------------------------------------
```

  : Example Command 0x15 - Enable Solenoid at Full Power

No response is expected. This is mostly used in older machines where
solenoids could be enabled without PWM.

### Disable Solenoid (0x16)

Disable solenoid. Payload is the solenoid index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to disable

  ------------------------------------------------------------------------
```

  : Payload of Command 0x16 - Disable Solenoid

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           22          Command 22 - Disable Solenoid

  1           1           25          Disable solenoid 25
  -----------------------------------------------------------------------
```

  : Example Command 0x16 - Disable Solenoid

No response is expected.

### Pulse Solenoid (0x17)

Pulse solenoid with it's configured pulse time. Payload is the solenoid
index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to pulse

  ------------------------------------------------------------------------
```

  : Payload of Command 0x17 - Pulse Solenoid

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           23          Command 23 - Pulse Solenoid

  1           1           25          Pulse solenoid 25
  -----------------------------------------------------------------------
```

  : Example Command 0x17 - Pulse Solenoid

No response is expected. Use command 0x18 to configure the pulse time.

### Set Solenoid Pulse Time (0x18)

Configure the pulse time of a solenoid in milliseconds. Payload is the
solenoid index and pulse time.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to configure

  2              1              Pulse time in ms (0-255)-
```

  : Payload of Command 0x18 - Set Solenoid Pulse Time

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           21          Command 24 - Set Solenoid Pulse
                                      Time

  1           1           25          Configure solenoid 25

  2           1           50          Set pulse time to 50ms
  -----------------------------------------------------------------------
```

  : Example Command 0x18 - Set Solenoid Pulse Time

No response is expected. This will affect pulses in command 0x17.

### Set Segment Display 0-6 (0x1E - 0x24)

Set content of segment display `d` 0-6. Payload is a null terminated
string. Content encoding depends on the type of the display (from
command 0x7).

```
  ---------------------------------------------------------------------------------------
  Byte        Length              Value               Comment
  ----------- ------------------- ------------------- -----------------------------------
  0           1                   30 + d              Command byte for set segment
                                                      depending on segment number `d`

  1           1                   `sw(sd) * bs(st)`   Bytes which will follow. Number of
                                                      segments (0-127) multiplied by
                                                      bytes per segment for this display
                                                      (1 or 2 bytes).

  2           `sw(sd) * bs(st)`   Number of segments  One or two bytes per segment for
                                  (0-127) multiplied  all segments. Encoding depends on
                                  by bytes per        segment type (see command 0x7).
                                  segment for this
                                  display (1 or 2
                                  bytes)----------------
```

  : Command 0x1E - 0x24 - Set Segment Display `d`

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           31          Command 31 - Set Segment display 1

  1           1           12          12 Bytes will follow

  2           12          Hello       Set display1 to hello world (ASCII
                          World!      type display)
```

  : Example Command 0x1E - 0x24 - Set Segment Display `d`

No response is expected.

### Get Status of Switch (0x28)

Get the status of a switch. Payload is the switch index:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `s` of the switch to query

  ------------------------------------------------------------------------
```

  : Payload of Command 0x28 - Get Status of Switch

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           40          Command 40 - Get Status of Switch

  1           1           25          Query status of switch 25
  -----------------------------------------------------------------------
```

  : Example Command 0x28 - Get Status of Switch

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              0=Off, 1=On, 2=Switch not existing

  ------------------------------------------------------------------------
```

  : Response to 0x28 - Get Status of Switch

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Status of switch is off

  -----------------------------------------------------------------------
```

  : Example Response to 0x28 - Get Status of Switch

MPF will read all switches at startup using this command.

### Get Changed Switches (0x29)

Check is switches changed. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           41          Command 41 - Get Changed Switches

  -----------------------------------------------------------------------
```

  : Example Command 0x29 - Get Changed Switches

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              127=No change. Otherwise: The numer of
                                changed switch. Bit 7 is the status of
                                that switch.

  ------------------------------------------------------------------------
```

  : Response to 0x29 - Get Changed Switches

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           10          Switch 10 turned off

  -----------------------------------------------------------------------
```

  : Example Response to 0x29 - Get Changed Switches

MPF will poll this at 100 Hz by default.

### Play Sound (0x32)

Play a sound on a hardware sound card. This is used to trigger sounds on
existing sound interfaces on older machines. The behavior of sounds
usually differs per sound number (looping/not looping/stop other sounds
etc) and cannot be influenced by the CPU.

Payload is the sound number.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Track to play (default track is 1)

  2              1              Index of sound to play
  ------------------------------------------------------------------------
```

  : Payload of Command 0x32 - Play Sound

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           50          Command 50 - Play Sound

  1           1           1           Play on track 1

  2           1           42          Play sound 42
  -----------------------------------------------------------------------
```

  : Example Command 0x32 - Play Sound

No response is expected.

### Stop Sound (0x33)

Stop the current playing sound.

Payload is the sound number.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Track to stop (default track is 1)-
```

  : Payload of Command 0x33 - Stop Sound

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           51          Command 51 - Stop Sound

  1           1           1           Stop all sounds on track 1
  -----------------------------------------------------------------------
```

  : Example Command 0x33 - Stop Sound

No response is expected.

### Play Sound File (0x34)

Play a sound file on external hardware. This is used to extend sound
capabilities on older machines in LISY. Alternatively, you can use the
MPF sound system.

Payload is a null terminated string containing track, flags and the
filename of the sound.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Track to play (default track is 1)

  2              1              Flags (bit 0=loop, 1=no cache)

  3              `n`            Filename (length `n`)

  3 + `n`        1              Null terminator
  ------------------------------------------------------------------------
```

  : Payload of Command 0x34 - Play Sound File

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           52          Command 52 - Play Sound File

  1           1           1           Use Track 1

  2           1           1           Loop file

  3           9           test.mp3    Play sound test.mp3. Last character
                                      is null byte.
  -----------------------------------------------------------------------
```

  : Example Command 0x34 - Play Sound File

No response is expected.

### Text to speech (0x35)

This is used to extend sound capabilities on older machines in LISY.

Payload is a null terminated string containing track, flags and the text
to play.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Track to play (default track is 1)

  2              1              Flags (bit 0=loop, 1=no cache)

  3              `n`            Text to play (length `n`)

  3 + `n`        1              Null terminator
  ------------------------------------------------------------------------
```

  : Payload of Command 0x35 - Text to speech

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           53          Command 53 - Text to speech

  1           1           Track to
                          play
                          (default
                          track is 1)

  2           1           1           No loop. Use Cache.

  3           6           Hello       Play text 'hello'. Last character
                                      is null byte.
  -----------------------------------------------------------------------
```

  : Example Command 0x35 - Text to speech

No response is expected.

### Set Sound Volume (0x36)

Set volume of amplifier. This may be connected either to a hardware
soundcard or to the output of the MPF sound system.

Payload is the sound number.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Volume in percent (0-100)

  2              1              Track to change (default track is 1)-
```

  : Payload of Command 0x36 - Set Sound Volume

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           54          Command 54 - Set Sound Volume

  1           1           Change
                          track 1

  2           1           50          Set volume to 50%
  -----------------------------------------------------------------------
```

  : Example Command 0x36 - Set Sound Volume

No response is expected.

### Init/Reset (0x64)

Reset and initialize the platform. MPF will expect this command to reset
all coil configs and to disable all coils and lights. Does not have any
payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           100         Command 100 - Init/Reset

  -----------------------------------------------------------------------
```

  : Example Command 0x64 - Init/Reset

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              0=OK. Otherwise an error code. MPF will
                                retry on error.

  ------------------------------------------------------------------------
```

  : Response to 0x64 - Init/Reset

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Reset ok.

  -----------------------------------------------------------------------
```

  : Example Response to 0x64 - Init/Reset

This will be the first command send by MPF.

### Watchdog (0x65)

Will be send every 500ms. The hardware is expected to disable all
solenoids and light if it did not get a watchdog for 1s. Does not have
any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           101         Command 101 - Watchdog

  -----------------------------------------------------------------------
```

  : Example Command 0x65 - Watchdog

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              0=OK. Otherwise an error code

  ------------------------------------------------------------------------
```

  : Response to 0x65 - Watchdog

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           0           Watchdog ok.

  -----------------------------------------------------------------------
```

  : Example Response to 0x65 - Watchdog

This be send periodically at 2 Hz in MPF.

## Protocol reference (v0.09) - RFC

This section contains a proposal for new methods. This is still in
development. Requests and commends are welcome. All commands are
considered in "Request for Comments (RFC)" state. They will likely end
up in v0.09 in some way.

### Get Count of Modern Lights (0x13)

Get count of modern lights available. Does not have any payload.

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           19          Command 19 - Get Count of Modern
                                      Lights

  -----------------------------------------------------------------------
```

  : Example Command 0x13 - Get Count of Modern Lights

Returns one byte:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  0              1              Light count `m` (0 to 255). 0 if no modern
                                lights exist.

  ------------------------------------------------------------------------
```

  : Response to 0x13 - Get Count of Modern Lights

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           128         Platform supports 128 modern lights
                                      with numbers 0 to 127.

  -----------------------------------------------------------------------
```

  : Example Response to 0x13 - Get Count of Modern Lights

MPF uses this number to refuse any lights with a number larger or equal
than `m` and subtype `light`. Return `0` if you do not support modern
lights in your platform.

### Fade Modern Light (0x0d)

Fade a group of modern lights.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `m` of the first light

  2              2              Fade time in ms (0-65535). Can be 0 to set
                                the brightness instantly.

  4              1              Number `n` of lights to fade. Can be 1 to
                                set or fade a single light.

  5              `n`            One byte of brightness per light (0-255).
                                `n` bytes in total
  ------------------------------------------------------------------------
```

  : Payload of Command 0x0d - Fade Modern Light

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           19          Command 13 - Fade Modern Light

  1           1           42          First light is 42

  2           2           50          Fade to color in 50ms.

  4           1           3           Fade three lights (i.e. RGB in
                                      sync)

  5           1           127         Fade light 42 to 50% brightness

  6           1           0           Fade light 43 to 0% brightness

  7           1           255         Fade light 44 to 100% brightness
  -----------------------------------------------------------------------
```

  : Example Command 0x0d - Fade Modern Light

No response is expected.

### Set Solenoid Recycle Time (0x19)

Configure the recycle time of a solenoid in milliseconds. The platform
will prevent any new pulse/enable until recycle time has passed after a
pulse end or disable. This prevents overheating through "machine
gunning" on pops, flaky switches or repeated pulses through bad code.
By default MPF will set recycle to two times the pulse time but it can
be changed.

Payload is the solenoid index and recycle time.

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to configure

  2              1              Recycle time in ms (0-255)-
```

  : Payload of Command 0x19 - Set Solenoid Recycle Time

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           25          Command 25 - Set Solenoid Recycle
                                      Time

  1           1           25          Configure solenoid 25

  2           1           50          Set recycle time to 100ms
  -----------------------------------------------------------------------
```

  : Example Command 0x19 - Set Solenoid Recycle Time

No response is expected. This will affect pulses, enables and all
hardware rules.

### Pulse and Enable Solenoid with PWM (0x1A)

Pulse solenoid and then enable solenoid with PWM. Payload is the
solenoid index, pulse time, pulse power and hold power:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to enable

  2              1              Pulse time in ms (0-255)

  3              1              Pulse PWM power (0-255). 0=0% power.
                                255=100% power

  4              1              Hold PWM power (0-255). 0=0% power.
                                255=100% power
  ------------------------------------------------------------------------
```

  : Payload of Command 0x1A - Pulse and Enable Solenoid with PWM

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           26          Command 26 - Enable Solenoid with
                                      PWM and Pulse

  1           1           25          Enable solenoid 25

  2           1           30          30ms initial pulse

  3           1           191         191/255 = 75% pulse power

  4           1           64          25% hold power
  -----------------------------------------------------------------------
```

  : Example Command 0x15 - Pulse and Enable Solenoid with PWM

No response is expected. This command can also be used to just pulse a
coil with PWM if "Hold PWM power" is set to 0.

### Configure Hardware Rule for Solenoid (0x3C)

Program a hardware rule into the controller to control a solenoid based
on one to three switches. This is used in modern machines to implement
low latency responses (because responding to switch hits in software
causes too much latency and jitter). There can be only one hardware rule
per solenoid. A new rule will always overwrite an old one for the
solenoid.

Flags decide what the three switches do:

```
  -----------------------------------------------------------------------
  Bit               Description
  ----------------- -----------------------------------------------------
  0                 When switch becomes active trigger the rule. Usually
                    set on the first switch to trigger the rule.
                    Sometimes a second switch is used just to disable a
                    rule (such as on EOS of a flipper).

  1                 When switch becomes inactive disable the rule. This
                    is what you want on flipper fingers but not on
                    slings/pops.

  2                 reserved

  3                 reserved

  4                 reserved

  5                 reserved

  6                 reserved

  7                 reserved
  -----------------------------------------------------------------------
```

  : Flags for Command 0x3C - Configure Hardware Rule for Solenoid

Payload is the solenoid index, one to three switches, pulse time, pulse
power, hold power and some flags:

```
  ------------------------------------------------------------------------
  Byte           Length         Description
  -------------- -------------- ------------------------------------------
  1              1              Index `c` of the solenoid to configure

  2              1              Switch `sw1`. Set bit 7 to invert the
                                switch.

  3              1              Switch `sw2`. Set bit 7 to invert the
                                switch.

  4              1              Switch `sw3`. Set bit 7 to invert the
                                switch.

  5              1              Pulse time in ms (0-255)

  6              1              Pulse PWM power (0-255). 0=0% power.
                                255=100% power

  7              1              Hold PWM power (0-255). 0=0% power.
                                255=100% power

  8              1              Flag for `sw1`

  9              1              Flag for `sw2`

  10             1              Flag for `sw3`
  ------------------------------------------------------------------------
```

  : Payload of Command 0x3C - Configure Hardware Rule for Solenoid

Example:

```
  -----------------------------------------------------------------------
  Byte        Length      Example     Comment
  ----------- ----------- ----------- -----------------------------------
  0           1           60          Command 60 - Configure Hardware
                                      Rule for Solenoid

  1           1           25          Configure rule for solenoid 25

  2           1           5           Use Switch 5 as `sw1`

  3           1           134         Use inverted Switch 6 as `sw2`

  4           1           127         No switch as `sw3`

  5           1           30          30ms initial pulse

  6           1           191         191/255 = 75% pulse power

  7           1           64          25% hold power

  8           1           3           `sw1` will enable the rule and
                                      disable it when released.

  9           1           2           `sw2` will disable the rule if it
                                      closes (because it is inverted).

  10          1           0           Do not use `sw3`
  -----------------------------------------------------------------------
```

  : Example Command 0x3C - Configure Hardware Rule for Solenoid

No response is expected. To disable a rule just set all flags to 0.
