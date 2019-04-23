# ArduLCD
Arduino based interface for HD44780 LCD to lcdproc/lcd4linux and equivalent.

# Beginnings

As time goes on, it is harder and harder to connected HD44780 LCDs to computer systems. It used to be that every computer had a parallel port, and you just wired it up there.  However fewer and fewer machines have parallel ports, and in some cases the software won't work (e.g. lcdproc on FreeBSD locks up if you try to use the parallel port). 

So having bought an arduino, and realising how cheap and powerful it was, I thought it would be a good fit for a LCD to USB interface. It is also a relatively simple first project. 

# Design

I decided to emulate the old "pic-an-lcd" interface. This was a (proprietery) serial to HD44780 interface that was used in the late 90's. I knew of it back then, but never bothered due to the abundance of parallel ports.  I decided to emulate this because:

1. It is old enough that it is supported by most LCD software. In particular the software I tend to use (LCDproc and lcd4linux)
2. It is serial based, so I can make use of the existing USB->Serial interface provided by the arduino.
3. It is relatively easy to interface to directly (i.e. by writing to the Serial port from your own program), so you need not use "LCD software" at all.

Because the Arduino is stupidly overpowered for such a simple task, I worked on making it fast. The HD44780 makes use of 8 bit transfers, and serial speed is at 57600 baud.

This code is nowhere near good, it is more like a hack than a proper development, but posted here in case someone else is interested in such a thing, and saves them the time to reverse engineer the protocol. The original "pic-an-lcd" page seems to have dropped off the internet (http://www.cyberramp.net/~dale) , so had to work out what it is doing from the LCDproc docs and other sources.


# Current status

-- 23/04/2019

After an issue raised about lack of wiring diagram, I decided to whip one up and provide it here. You can find it in the "resources" folder. It was done with gschem (http://www.geda-project.org/), and feel free to make improvements. The diagram includes a transistor, capacitor and wiring between the backlight and pin 10 for dynamic backlight brightness control. This is optional, and can be omitted if you are not interested in that capability.

If you do build in the PWM control circuitry, then you can utilise the old "GPIO" command in pic-an-lcd. This will allow you to vary the brightness of the backlight from 0 (off) to 255 (100%) via a serial command. Otherwise you can fix the brightness of your backlight at compile time by changing the "STARTUP_BRIGHTNESS" define in the code.


-- 02/05/2016

We have a first working version of ArduLCD. Supports HD44780 displays using the pic-an-lcd driver of LCDd (tested on version 0.5.5).

-- 02/04/2016

Well, I found a user manual for the original PIC-an-LCD: http://www.phanderson.com/PIC-n-LCD/user_manual.html

Turns out the protocol is far more advanced than I expected, allowing for quite a bit of control, higher level LCD functions, and the ability to either output a tone from a speaker, or to fire off 4 GPIO pins.

In addition, after looking at the lcdproc source code, none of the advanced features are used. lcdproc basically just uses the "raw passthrough" escape characters to talk to the lcd directly.

# Known bugs

Currently there are no known bugs. It seems to work fine on my test rig, and the first proper implementation on my server seems to work fine (blue 20x4 LCD, LCDd 0.5.5 on FreeBSD). Feel free to give it a test yourselves to see if you come across any problems. Feel free to raise a ticket if you come across anything. 

# Building

I don't know about you, but I use "ino" on the command line. cd to the root of the repo dir, and run "ino build", when completed successfully, run "ino upload" to push to arduino.

commands to run for install (I use Devuan/Debian, alter to taste for your specific distro):
```
apt install arduino-mk
pip install ino --user 
```


## To Change the display size (default: 24x2)

At the moment you have to edit the code directly, there are two constants you have to change LCDW and LCDH, which govern the LCD dimensions in character width and height respectively

# Schematic

Find it in "resources", called "wiring_schematic.sch". There is a png version too if you just want to view without editing, "wiring_schematic.png"
