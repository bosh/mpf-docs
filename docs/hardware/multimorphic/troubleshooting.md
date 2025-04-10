---
title: Troubleshooting P-Roc/P3-Roc
---

# Troubleshooting P-Roc/P3-Roc


If you got problems with your hardware platform we first recommend to
read our
[troubleshooting guide](../../troubleshooting/index.md). Here are some hardware platform specific steps:

## P/P3-Roc Does Not Show Up In Device Manager or dmesg Log

If your P/P3-Roc does not show up in device manager (Windows) or does
not create a line in `dmesg` or `lsusb` (Linux/Mac) have a look at the
USB cable and connection. Bad cables are a thing (especially for longer
cables). Try removing USB hubs.

Is the board powered up? Are the four blue LEDs circling? If not check
your power supply.

## ImportError in MPF Log

If you see something along this in your log:

``` console
in <module>
   from mpf.platform.pinproc.x86.python36 import pinproc

ImportError: DLL load failed: The specified module could not be found
```

This usually means that the FTDI libs are not installed in the correct
version. On Linux pinproc might not be installed at all. On Windows
`Visual C++ Redistributable for Visual Studio` might also be missing in
the correct version. Have a look a the
[install instructions for your OS](hardware_drivers.md) to find and install the correct requirements.

## Failed to reset P/P3-Roc

If you see this repeatedly in the log of MPF:

``` console
Failed to reset P/P3-Roc: OSError: Error in WriteData: wrote 0 of 8 bytes. Is your P/P3-Roc connected and powered up?
Will retry creating PinPROC and resetting it in 1s.
```

This usually means that the P/P3-Roc is either not powered up or not
connected.

## Random Crashes of MPF

You might see errors such as the following (usually in
`p_roc_common.py`):

``` console
OSError: Error in WriteData: wrote 0 of 8 bytes
```

This error is triggered by communication issues with the P/P3-Roc. Often
this is caused by an unreliable power supply or overload on the 5V rail
of that supply. This might also be caused by a bad USB cable. In any
case you should also find USB communication errors in your operating
system which might give you further clues.

## Run Hardware Scan

Using `mpf hardware scan` you can find out if your P/P3-Roc is talking
properly to MPF using USB. Additionally, it will show you which `SW-16`
are connected:

``` console
$ mpf hardware scan

Firmware Version: 2 Firmware Revision: 6 Hardware Board ID: 1
SW-16 boards found:
 - Board: 0 Switches: 16 Device Type: A3 Board ID: 0
 - Board: 1 Switches: 16 Device Type: A3 Board ID: 1
 - Board: 2 Switches: 16 Device Type: A4 Board ID: 2
```

Unfortunately, MPF cannot know which `PD-16` or `PD-LED` are connected
as this information is not available. See
[firmware_upgrade](../../running/commands/hardware.md) for details.

## Enable Debugging

If you got problems with your platform try to enable `debug` first. As
described in the
[general debugging section](../../troubleshooting/general_debugging.md) of our
[troubleshooting guide](../../troubleshooting/index.md) this is done by adding `debug: true` to your `p_roc` config
section:

``` yaml
p_roc:
  debug: true
```

This will add a lot more debugging and might slow down MPF a bit. We
recommend to disable/remove it after finishing debugging.

## Enable Bus Tracing

If your hardware behaves different from the way you told it to in MPF or
if you are seeing lags or delays it might be wise to turn on bus
tracing.

``` yaml
p_roc:
  debug: true
  trace_bus: true
```

This logs all calls to libpinproc. This will cause a lot of additional
log lines and might considerably slow down MPF. Definitely disable this
after you finished debugging.

## Upgrade the Firmware of Your P/P3-Roc

If you experience problems around hardware rules or such consider
upgrading your P/P3-Roc firmware. Sometimes bugs in the firmware get
fixed or stuff becomes more robust. For some known cases MPF will crash
intentionally and tell you to upgrade but there might be cases which we
do not know.

See [How to update the Firmware of the P-Roc or P3-Roc](firmware_upgrade.md) for details about
firmware upgrades.

## Switches Are Not Registering

If your coils are working but switches are not registering please check
the following points:

### On the P-Roc

* Is 12V power available? This will disable switches but not much
    else.
* Is ground connected properly to your switches?

### On the P3-Roc

* Do your SW-16 show in `mpf hardware scan`? (see above)
* If not: Is the SW-16 bus connected properly (and not twisted)?
* Is ground connected properly to your switches? Should be connected
    to pin 10 on J2 or J6 of SW-16.

## Some Drivers Are Not Working

If some drivers are working but other are not.

### On the P-Roc

If you see the following message on your console (not log; you might
have to use the `-t` commandline flag to see them):

``` doscon
Refusing to update driver #144; polarity differs on non-custom machine.
```

This means that the polarity which is defined for your machine type
(i.e. WPC) does not match your driver definition. If you see this please
tell us in the MPF user forum and we will investigate this with you.

## All Coils Turn On When I Power Up My Machine

If this happens and MPF is not yet running you likely do not have common
ground between high voltage and logic power. Turn your machine off and
only turn it back on when you have fixed and verified common ground.
Read the section about
[common ground](../voltages_and_power/voltages_and_power.md) for details or consult an electrical engineer.

If this happens shortly after MPF started and you are using a P-Roc this
might have to do with the polarity of your coils. Check the `polarity`
setting and make sure you configured the correct machine type as there
are different defaults in different machine types.

In any case we recommend that you test this with either less voltage
(i.e. 12V instead of 48V) or by using lamps instead of coils on your
outputs as that will prevent hardware damage due to overcurrent.

## Serial Bus Issues

### Bad Cables/Interference

Each serial bus connector has a + and a - pin. The serial cables connect
from board to board like jumper-cables + to + and - to -. Connecting
ground pins on the serial bus is not required. A bad serial cable can be
difficult to diagnose, particularly if it is the first serial cable in a
chain as it will prevent signals to all boards downstream of the bad
connection. One clue that a bad serial cable is present is if some of
the boards function but others do not. Another clue which is sometimes
present on the driver bus is discovered looking at the driver boards
watchdog timer indicator. On the PD-LED the watchdog is indicated by a
lit diode D3. On the PD-16 it is diode D11. The watchdog turns off when
the board is receiving signal over the driver bus from the P3-ROC when
MPF is running (including attract mode). If wiggling serial cables
causes the watch dog to light, a loos connection or bad cable is
present. While the switches bus does not have an equivelent watchdog,
the game's switch status screen can be monitored while wiggling cables
looking for a loose connection. It is possible for the vibration of a
mechanism (notoriously from a pop bumper) to cause intermittent faults
in a poorly connecting serial cable. Such intermittent faults are
difficult to diagnose.

### Termination

The P3-ROC interfaces to the playfield through two serial busses. The
switches serial bus connects SW-16 boards through J11 and/or J14. The
driver serial bus connects PD-16 and PD-LED boards through J12 and J15.
The serial busses are designed to allow boards to be connected in a
daisy chain fashion to each plug. A sourse of unreliable communication
on the buses is improper termination. The last board on each chain (not
to be confused with the board with the highest address) should have
dipswitch 8 set to ON. For example is the switches serial bus has 6
boards with J11 connecting to board A B and C and J14 connecting to
boards D, E and F, dipswitch 8 should be set to ON on boards C and F and
set to OFF on all other SW 16 boards. (Terminating board B would prevent
communication from board C on that side of the chain.) The same
termination strategy also applies to driver boards. For example if a mix
of PD-LED and PD-16 boards connect through J15 as A, B, C, D, and E with
E being the last board, board E would have dipswitch 8 set to ON.

Additionally, the P3-ROC board itself also has termination dip switches
(7 and 8) for the switches serial bus plugs. These should be set to ON.
There are no termination dip switches for the driver bus on the P3-ROC
board.

## Correct Addressing

Each of the SW-16 boards requires a unique binary address which is set
by the board's dipswitches 1 through 6. Although the P3-ROC has two
serial switch connectors (J11 and J14) there is only one serial switch
bus. Meaning, if one SW-16 board connects to the P3-ROC through J11 and
another through J14 the SW-16 boards will still require separate
addresses to be properly registered.

Similarly, the PD-16 and PD-LED driver boards also each require an
unique address on the driver bus accessed through J12 and J15 on the
P3-ROC. If for instance a PD-16 and a PD-LED share on the same address,
commands through the driver serial bus meant to drive LEDs can acutate
coils even if the boards are interfacing through different plugs.

On the SW-16, PD-16 and PD-LED boards themselves dipswitch addressing is
somewhat counterintuitive. Switch one is the lowest address bit and on
the SW-16 switch 6 is the highest. Reading the switch block from left
(starting at switch 1) to right, binary address zero would be 000000,
address one through four would be 100000, 010000, 110000 and 001000,
respectively. The PD-LED sets addresses on dipswitches 1 through 5 and
the PD-16 uses dipswitches 1 through 4 giving these boards fewer address
possibilities than the SW-16 which uses switches 1 through 6.

## Coils Are Not Firing

What to do if your coils are not working?

--8<-- "troubleshooting_coils.md"

--8<-- "troubleshooting_lights.md"

--8<-- "troubleshooting.md"
