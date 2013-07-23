# GPIO - General Purpose I/O

The GPIO functionality is the bread and butter of programming
microcontrollers. Microcontrollers have a number of pins (yes, the
little pins sticking out around the side of chip package) that can be
turned on or off and that can sense a voltage attached to them. Note
that a number of the LPC1343s pins are also support other
functionality...

To get started, switch into the `examples/blink` directory of the sdk
you checked out in the last lesson, open the file `main.c` and read
through the comment. Compile and run the code.

The `blink` example uses some extra code (located under `libs/anypio`)
that makes it trivial to turn pins on and off without having to write a
lot of boiler plate code. Once you've had a look at `blink`, have a look
at the example under `blink_pro`. This includes a bit of the necessary
boiler plate as well as using the systick interrupt to control the LED.

Continue reading here once you've worked through the examples and maybe
tried changing a thing or two ...

## Down and dirty boilerplate

To understand how IO really works on the processor, you'll need a copy
of the LPC1343
[usermanual](http://www.nxp.com/documents/user_manual/UM10375.pdf). Scan
through Chapter 9. If you've never read a datasheet or microcontroller
user manual before, this will see very intimidating and you probably
won't understand most of what you read, don't worry about it for now,
things will start making sense.


Configuring a microcontroller usually involves writing to a memory
location and manipulating values. These memory locations are accessed
just like normal RAM memory, but they are not connected to RAM, but
instead to physical peripherals of the chip (just pins for now...)

If you're not familiar with bit operation, binary numbers and
hexadecimal, please read the bits_bytes_and_nibbles.md chapter to brush
up...

The physical pins on the Anykey are organized into 4 "ports", each port
controls 12 pins. Each port is mapped into memory and contains a data
structure that looks like this:

    typedef struct {
    	HW_RW MASKED_DATA[0xfff];	//bit masked access to pins
    	HW_RW DATA;			//all 12 pins
    	HW_RS RESERVED1[0x1000];	//reserved (byte offsets 0x4000-0x7fff)
    	HW_RW DIR;			//direction bitmap. 0=in, 1=out
    	HW_RW IS;			//interrupt sense bitmap. 0=edge, 1=level
    	HW_RW IBE;			//interrupt both edges bitmap. 0=see IEV, 1=both edges trigger
    	HW_RW IEV;			//interrupt event bitmap. 0=falling edge, 1=rising edge
    	HW_RW IE;			//interrupt mask bitmap. 0=interrupt off, 1=on
    	HW_RO RIS;			//raw interrupt state bitmap, not masked by IE
    	HW_RO MIS;			//masked interrupt state bitmap, masked by IE
    	HW_WO IC;			//interrupt clear bitmap. Write 1 to clear edge interrupt mask
    	HW_RS RESERVED2[0x3f8];		//reserved (byte offsets 0x8020 - 0x8fff)
    	HW_UU UNUSED[0x1c00];		//padding to next GPIO bank (byte offsets 0x9000 to 0xffff)
    } GPIO_STRUCT;

This definition is taken from the file `anykey/memorymap.h` and mirrors the
specification in the user manual, see. Chapter 9.4. 

Every element in the struct above serves a specific function for
writing, reading and configuring the port. For example: GPIO.data
is a 32 bit value. The bottom 12 bits represent the state of the ports
12 pins. If a pin is configured for OUTPUT, writing a value to these
bits turns the pin on. If it's configured for INPUT the value of the
pin's bit will be either one or zero depending on whether there is
voltage applied to the pin.


## OUTPUT ... for now

The blink example only needs output, so we will concentrate on that for
now. The DIR member of the struct is responsible for configuring the pin
as output or input. If the bit corresponding to the pin we want to
use is set to 0 it's configured as output, if it's set to 1, the pin is
an input pin. 

Our processors  contains one of the GPIO_STRUCT (see above) memory
regions for each of it's 4 ports. These start at memory location
`0x50000000`, `anykey/memorymap.h` contains the following:

    #define GPIO ((GPIO_STRUCT*)(0x50000000))

which means: define an alias named GPIO which is a pointer (\*) to a
region of memory starting at 0x50000000. The contents of the region
corresponding to the type GPIO_STRUCT. The way C works, a pointer to a
region of memory can be used as an array, so if we want to access the
configuration for the second port, we can do so via:

     configuration_of_second_part = GPIO[1]

The first element of an array has the number 0, so the second element
has number 1 ...

In order to configure third pin of the second port to output, the third
bit of the DIR element of the second port configuration to 1:

    GPIO[1]->DIR = (GPIO[1]->DIR) | (1 << 3)

and in order to write a value (1) to the pin we need to set the
corresponding bit in the DATA element to 1:

    GPIO[1]->DATA = (GPIO[1]->DATA) | (1 << 3)

All this is basically handled for you when you use the functions defined
in `anykey/gpio.h` these functions are really just a wrapper to save you
some typing, but it's important to know what is going on under the hood.

For some of the pins of the Anykey, it's also important to set the
function to PIO before using them, because by default they may be
configured to provide a different functionality. We won't get into the
bits and bytes, but the functions to do this are listed below:

    Function             | Description                    | Example
    any_gpio_set_dir     | set the direction of a pin     | any_gpio_set_dir(port, pin, OUTPUT)
    any_gpio_write       | write 0 or 1 to a pin          | any_gpio_write(port, pin, true)
    any_gpio_read        | read the value of an input pin | value = any_gpio_read(port, pin)
    ANY_GPIO_SET_FUNCTION| configure function of a pin    | ANY_GPIO_SET_FUNCTION(port, pin, PIO, IOCON_IO_ADMODE_DIGITAL);







