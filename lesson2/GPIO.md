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

## Some Theory: Bits, bytes, nibbles, binary, hex & co.

Since the low-level configuration of a microcontroller involves dealing
with bits and bytes, we'd like to provide a short introduction of how to
deal with low levl data structures.

The basic unit of memory in almost all microcontrollers is a byte which
consists of 8 bits. Each bit can be 1 or 0, so one bit can represent two
values (0 and 1), 2 bits can represent 4 values ( 00, 01, 10 & 11), 3
bits 8 values and so one. In short X number of bits can represent 2^X
different values (2 * 2 * 2 * ... ) <- repeat X times. A byte (8 bits)
can represent 256 different values.

These different values are usually interpreted as numbers:

    bit#:    7 | 6 | 5 | 4 | 3 | 2 | 1 | 0
    value:  127| 64| 32| 16| 8 | 4 | 2 | 1

Each bit is assigned a value, above and in order to translate from bit
values to number values, you just need to add up all the values in the
positions where a bit is set to 1.
    

    bit#     :   7 |  6 |  5 |  4 |  3 |  2 | 1 | 0
    num value:  127| 64 | 32 | 16 |  8 |  4 | 2 | 1
    bit value:   0 |  0 |  0 |  0 |  0 |  0 | 0 | 0

A byte consisting of all zero's like above has the value 0. If a single
bit is set:

    bit#     :   7 |  6 |  5 |  4 |  3 |  2 | 1 | 0
    num value:  127| 64 | 32 | 16 |  8 |  4 | 2 | 1
    bit value:   0 |  0 |  0 |  1 |  0 |  0 | 0 | 0

The value of the byte corresponds to the value of the set bit (16 in the
example above). If several bits are set to one:


    bit#     :   7 |  6 |  5 |  4 |  3 |  2 | 1 | 0
    num value:  127| 64 | 32 | 16 |  8 |  4 | 2 | 1
    bit value:   0 |  0 |  0 |  1 |  0 |  1 | 1 | 1

The value of the byte is the sum of the values corresponding to the set
bits. In the example above: 16 + 4 + 2 + 1 = 23;

If all bits are set, the value is 127 + 64 + 32 + 16 + 8 + 4 + 2 + 1 =
256.

To represent larger values you can use values cnosisting of more than
one byte.

It's important to remember, though that this mapping of bits to numbers
is just an arbitray convention. A byte just represents 256 different
values, whether these values represent the number 0-255 or 256 different
colors is irrelevant. E.g. when configuring a microcontroller, the eight
bits in a byte are mapped to pins and each bit indicates whether a pin
is turned on or off. Another common thing that a series of (32 on our
processor) bits can represent are locations in memory.

### Signed and unsigned

Also a byte doesn't necessarily have to represent
the values 0-255, it could also be configured as a "signed" type,
representing the numbers -128 to 127. C defines "types" which are meant
to indicate what a series of bits is supposed to represent, e.g. uint8_t
is a type ("_t") that is unsigned ("u") representing a number ("int")
that is 8 bits long.

|| Type     || Description        || Values|
| uint8_t   | unsigned 8 bit type | 0 ... 255  |
|  int8_t   | signed 8 bit type   | -128 ... 127 |
| uint16_t  | unsigned 16 bit     | 0 ... 65,535  |
|  int116_t | signed 16 bit       | −32,768 ... 32,767 |
| uint32_t  | unsigned 32 bit     | 0 ... 4,294,967,295 |
|  int32_t  | signed 32 bit type  |  −2,147,483,648 ... 2,147,483,647 |

and so on... Basically a signed value can represent the values:

    -(2^ (number of bits - 1) ... (2 ^ (number of bits-1) ) - 1

Unsigned values can represent values form:

    0 ... (2 ^ (number of bits -1)) - 1


### Math (ugh)

When a type represents a number, we can perform mathematical operations
with it, using the operators (+, -, *, /) the star is the multiplication
operator and the slash division.

### Enough math, using values as a series of bits.

Another class of operations that can be perform with values are
"logical" operations:

|| Operation ||  Operator || Name     || Description ||
|  AND       |  * | logical and  | equals 1 if both values are one |
|  OR        |  \| | logical or   | equals 1 if either value is one |
|  XOR       |  ^ | exclusive or | equals 1 if one of the values is one |
|  NOT       |  ~ | negation     | equals 1 if value is 0 and 0 if value is 1 |

You probably either know the table above, or it's not particularly
helpful, some examples:

     AND two bytes together

     byte 1 : 0 0 0 0  1 0 0 0
     byte 2 : 1 0 0 0  1 0 0 1
     result : 0 0 0 0  1 0 0 0

Since only



Configuring a microcontroller usually involves writing to a memory
location and manipulating values. These memory locations are accessed
just like normal RAM memory, but they are not connected to RAM, but
instead to physical peripherals of the chip (just pins for now...)

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

This definition is taken from the file `anykey/memory.h` and mirrors the
specification in the user manual, see. Chapter 9.4. 

Every element in the struct above serves a specific function for
writing, reading and configuring the port. For example


## OUTPUT ... for now

The blink example only needs output, so will concentrate on that for
now.


