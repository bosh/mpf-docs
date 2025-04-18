---
title: RGB.DMD Controller
---

# RGB.DMD Controller


Related Config File Sections:

* [hardware:](../config/hardware.md)
* [rgb_dmds:](../config/rgb_dmds.md)
* [smartmatrix:](../config/smartmatrix.md)
* [displays:](../config/displays.md)

The RGB.DMD controller was designed as a variant to the SmartMatrix that
would be capable of both controlling RGB LED panels and accepting and
decoding the DMD signal from an existing commercial pinball machine. As
soon as RGB LED panels with spacing matching that of a DMD became
available in September of 2015, Eli worked with the MPF developers to
modify the SmartMatrix software and make it possible to stream color DMD
images from MPF to SmartMatrix and RGB.DMD displays so that MPF machines
can have color displays in the traditional 32x128 DMD form factor.

It's likely that no one would be using RGB LED DMDs if it wasn't for
the efforts of Eli Curtz. Eli first posted about these types of panels
in the P-ROC forum (now defunct) in 2014. At that time we could only
find panels with 3mm spacing between pixels which was a bit larger than
traditional pinball DMDs, but that's what kicked off the conversation
about, "Whoa, maybe we could use these for 'real' color DMDs some
day." Then in September 2015, Eli posted again telling us that we could
now get panels with 2.5mm spacing which is the perfect size we need. Eli
also showed us how to connect them and what software we needed to make
everything work. So really everything here is because of Eli. All we did
is take everything he showed us and write it down. (Well, that and we
also created the interface for MPF, but that was the easy part.) So
thanks Eli!

The Eagle files are available along with the code for those who'd like
to build their own [RGB.DMD board](https://github.com/ecurtz/RGB_DMD).
Connect your panels and you are good to go.

In MPF, RGB.DMD works just like
[How to configure a "SmartMatrix" RGB LED DMD](smartmatrix.md) (go there
for details). Can copy the following example (and replace `com12` with
your com port):

``` yaml
hardware:
  rgb_dmd: smartmatrix
smartmatrix:
  smartmatrix_1:
    port: com12
    baud: 3000000
    old_cookie: false
rgb_dmds:
  smartmatrix_1:
    platform: smartmatrix
    source_display: dmd
```

## What if it did not work?

Have a look at our [hardware troubleshooting guide](troubleshooting_hardware/index.md).
