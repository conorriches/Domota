# Domota - a doorbot by cone
A modular, generic, and flexible DIN rail mounted entry system.
Ideal for hackerspaces and makerspaces!

## System Overview
Places need entry systems but commercial ones often are sub par, expensive, or have the logic on the insecure side.
This aims to enable a DIY approach using generic parts, easily swappable in case someone lets the magic smoke out, and easy to maintain by people without much in depth knowledge.

**The system is modular** - it's made up of physical modules that sit on a DIN rail to keep them tidy.
Modules communicate over i2c - this only works because the modules are physically close to one another.
As each module has the option of a relay, it's possible for a lock to be connected in parrallel/series (depending on whether you use a fail-safe or fail-secure lock) with each relay, so that any module can unlock or lock the door.
Additionally, you can use the second LED to indicate status instead of an error - just change the LED colour and maybe add a label to the PCB)

**The system is generic** - the hardware files allow you to choose what MCU and outputs you solder in. 
This means you can make a batch of boards and customise to fit your scenario.
You can print out labels for the front of each module to describe what it is and what the LEDs mean.

**The system is flexible** - as it talks over i2c, you can add more or fewer modules.
You aren't constrained once the code is written.



## Parts
### Generic hardware interface board

With 8 IO terminals, and more for power and auxiliary outputs, and the possibility of using either an Arduino Nano or Wemos D1 mini inside, this allows you to connect generic things that can then hook to/from the system:
* Wiegand/RFID readers
* Keypads
* Swipe card readers
* Relays
* LED's
* Logic outputs for other systems

#### Hardware Files
CNMB3-A and CNMB3-B are the two physical boards. They fit, unsurprisingly, into the CNMB3 range of enclosures from Camden Boss which can be procured from Rapid.
The A board is the lower board, with the B board being mounted by the enclosure window. 
The hardware outputs:
* Green LED - means that 3v3 is available, i.e. a board is plugged in and the voltage regulator is working
* Red LED - controllable from A0, perfect for blinking the error LED to indicate codes
* Relay - this can be fired on D8. 

#### Software Files
The software is in dev but is extremely basic. Examples will be uplaoded here when they're installed onto the prototypes. 
So far I've got a Wiegand reader to communicate a card ID to the board.
The library for I2c (Wire.h) is simple to use so connecting the two up will be easy.

#### Configuration
You can mount and install any, all, or no components. It's entirely possible therefore to:
* Have a keypad which connects to an Arduino Nano, which messages the inputted code over i2c.
* Have a fob reader which conencts to a Wemos d1 mini which checks the fob ID at an API, and fires a relay if valid.
* Have logic level IOs relating to the state of the system (door open/closed, door locked, request to exit button, emergency button used...) This can be relayed over I2c, web (if using wemos) or control other IOs.
* Have a single relay output. Relay output fires on a given i2c message or web status (i.e. remote unlocking module)

This is all delightfully modular so the system can be grown and changed based on requirements, all from the generic PCBs.

### Controller
This can be anything, but will be a Pi, because Pi.
This is the master in i2c communication if using it, but it can be any board that can do i2c. 
The controller will oversee the various IOs and make decisions as needed. This breaks logic out and separates concerns nicely. 

#### Hardware Files
The Pi will live in CNMB9 module. This is basically just a base board for a Pi to plug into (upside down, GPIO headers to the board) and a power supply:

12v UPS comes in from the existing PSU box. 
This will rectify to 5v for modules and for the Pi.
The board will just breakout the essential pins

Optional - this PSU could be a separate board inside the controller case. This would mean if using mains (tasty) you could make a 230vAC-5vDC supply, and plug it into the standard base board.

#### Software Files
The software has been written for a previous iteration of this project, but will need re-writing as the previous version communicated over Serial via USB. 
This will need changing to i2c.
