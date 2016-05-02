Motivation
----------

This library has been created for the [Robot Club Aachen E.V.] [RCA]. Two years ago
when we had to choose an internal bus system, the choice fell on the
CAN bus, because it is actually ideal for this application.
There was unfortunately no open source libraries for the control of
common CAN controller, so we implemented it ourselves.

The library is now in use for some time and has been extended from MCP2515
to other CAN controllers.


Features
--------

* Support for MCP2515
* Support for SJA1000 (for AVRs with or without external bus interface)
* Support for AT90CAN series
* Build as a library, so only used functions are compiled
* Low resource consumption

The library needs about 1500 bytes flash with all functions. Using no dynamic
filters 370 bytes less.

### Filters

For the MCP2515 there are two types of masks and filters available:
"statically" with a list that is created at compile time or
"dynamically" during runtime, but then with a larger Flash consumption.

The AT90CANxxx only has dynamic filters.

The same applies to the SJA1000. Although it theoretically offers two filters for
standard messages or for Extended messages, in practice these are more or less useless.


Integration into your own program
-----------------------------

First, the library needs to be created: adjust the file 'config.h' in the
`src/` folder for the right configuration and adjusts the Makefile to use
AVR type.

Then, the library can be built:

    $ make lib

Who uses WinAVR can also simply in Programmers Notepad one of the files
`Open from the `src/` folder and then select "Tools> [WinAVR] Make All".

Now a file 'libcan.a' should have arisen. Together with 'can.h' and
'config.h' these files can now be copied to your project directory.

In order to really use the functions you must tell the Linker that it should
include the library. In the standard WinAVR Makefile you will need to add
the following line (266ff by line):

    ...
    LDFLAGS += $(patsubst %,-L%,$(EXTRALIBDIRS))
    LDFLAGS += $(PRINTF_LIB) $(SCANF_LIB) $(MATH_LIB)

    LDFLAGS += -L. -lcan        # To add

The linker then searches automatically in the current directory for the file
libcan.a and adds (only) the required functions into the source code.

#### How to use the ports:

Since AVR's I / O pins have multiple functions, please pay attention to the
Fusebits of your AVR. A x concrete example for ATmega32:

Port C is divided Bit 2 - 5 with the JTAG interface, which is enabled by default - disable!
Port D divides bits 0 and 1 to the serial port. If you want to use all pins, you can not use the MAX 232.

At last set the source of the oscillator frequency correctly and you can start using the CAN:

ATmega32 Low Fuse: 0xEF (External Crystal)
ATmega32 High Fuse: 0xD9 (JTAG Disabled)


Testprogram
-----------

A Program which simply sends a message via CAN could look like this:

int main(void)
{
	// Initialize the MCP2515
	can_init(BITRATE_125_KBPS);

	// Create a test message
	can_t msg;

	msg.id = 0x123456;
	msg.flags.rtr = 0;
	msg.flags.extended = 1;

	msg.length = 4;
	msg.data[0] = 0xde;
	msg.data[1] = 0xad;
	msg.data[2] = 0xbe;
	msg.data[3] = 0xef;

	// Send message
	can_send_message(&msg);

	while (1) {

	}
}


License
------

The library is licensed under a BSD license and may therefore be used freely.

If someone makes some modifications I would ask to let me or anyone else from
the [Robot Club] [RCA] get this, so that we all can benefit from it.

[rca]: http://www.roboterclub.rwth-aachen.de/
[MCP2515]: http://www.kreatives-chaos.com/artikel/ansteuerung-eines-mcp2515
[BSD-Lizenz]: http://de.wikipedia.org/wiki/BSD-Lizenz
