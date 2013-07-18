# Some Theory: Bits, bytes, nibbles, binary, hex & co.

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
255.

To represent larger values you can use values cnosisting of more than
one byte.

It's important to remember, though that this mapping of bits to numbers
is just an arbitray convention. A byte just represents 256 different
values, whether these values represent the number 0-255 or 256 different
colors is irrelevant. E.g. when configuring a microcontroller, the eight
bits in a byte are mapped to pins and each bit indicates whether a pin
is turned on or off. Another common thing that a series of (32 on our
processor) bits can represent are locations in memory.

## Signed and unsigned

Also a byte doesn't necessarily have to represent
the values 0-255, it could also be configured as a "signed" type,
representing the numbers -128 to 127. C defines "types" which are meant
to indicate what a series of bits is supposed to represent, e.g. uint8_t
is a type ("_t") that is unsigned ("u") representing a number ("int")
that is 8 bits long.
    
    |  Type     |  Description        |             Values                |
    |-----------|---------------------|-----------------------------------|
    | uint8_t   | unsigned 8 bit type |           0 ... 255               |
    |  int8_t   | signed 8 bit type   |         -128 ... 127              |
    | uint16_t  | unsigned 16 bit     |          0 ... 65,535             |
    |  int116_t | signed 16 bit       |        −32,768 ... 32,767         |
    | uint32_t  | unsigned 32 bit     |        0 ... 4,294,967,295        |
    |  int32_t  | signed 32 bit type  |  −2,147,483,648 ... 2,147,483,647 |

and so on... Basically a signed value can represent the values:

    -(2^ (number of bits - 1) ... (2 ^ (number of bits-1) ) - 1

Unsigned values can represent values form:

    0 ... (2 ^ (number of bits -1)) - 1

We'll leave out the details of how negative numbers are represented
using bits for now. In case you are interested, google for "two's
complement".

### Math (ugh)

When a type represents a number, we can perform mathematical operations
with it, using the operators (+, -, *, /) the star is the multiplication
operator and the slash division.

### Logical Operation : Enough math, using values as a series of bits.

Another class of operations that can be perform with values are
"logical" operations:

    | Operation  |  Operator |  Name        | Description                                |
    |------------|-----------|--------------|--------------------------------------------|
    |  AND       |  &        | logical and  | equals 1 if both values are one            |
    |  OR        |  \|       | logical or   | equals 1 if either value is one            |
    |  XOR       |  ^        | exclusive or | equals 1 if one of the values is one       |
    |  NOT       |  ~        | negation     | equals 1 if value is 0 and 0 if value is 1 |
    | SHIFT LEFT |  <<       | shift        | move the entire value left, filling up the new spaces with 0's|
    | SHIFT RIGHT|  >>       | shift        |  ...                                       |

You probably either know the table above, or it's not particularly
helpful, some examples:

     AND two bytes together
     (result = 3 & 137)
     
     bit num: 7 6 5 4  3 2 1 0
     -------------------------
     byte 1 : 0 0 0 0  1 0 0 0
     byte 2 : 1 0 0 0  1 0 0 1
     -------------------------
     result : 0 0 0 0  1 0 0 0

Since only both the bits at position 3 are set to one, that is the only
bit set in the result.

     OR two bytes together
     ( result = 6 | 172 )

     bit num: 7 6 5 4  3 2 1 0
     -------------------------
     byte 1 : 0 0 0 0  1 1 0 1
     byte 2 : 1 0 1 0  1 1 0 0
     -------------------------
     result : 1 0 1 0  1 1 0 1

Since one of each bits in position 7, 5, 3, 2 and 0 are set, those are
the bits set in the result.


     XOR two bytes together
     (result = 5 ^ 173)

     bit num: 7 6 5 4  3 2 1 0
     -------------------------
     byte 1 : 0 0 0 0  1 1 0 0
     byte 2 : 1 0 1 0  1 1 0 1
     -------------------------
     result : 1 0 1 0  0 0 0 1

For xor, only the bits in the result are set that correspond to bits
set EITHER in the first or second operant. Positions 3 and 2 where the
bits are set in BOTH operants are not set in the result.

    NOT works with only one operand 
    (result = ~5)
    
    bit num: 7 6 5 4  3 2 1 0
    -------------------------
    byte 1 : 0 0 0 0  1 1 0 0
    -------------------------
    result : 1 1 1 1  0 0 1 1

Finally shift:

    SHIFT left by 1 
    ( result = 5 << 1 )

    bit num: 7 6 5 4  3 2 1 0
    -------------------------
    byte 1 : 0 0 0 0  1 1 0 0
    -------------------------
    result : 0 0 0 1  1 0 0 0


    SHIFT right by 2 
    ( result = 5 >> 2 )

    bit num: 7 6 5 4  3 2 1 0
    -------------------------
    byte 1 : 0 0 0 0  1 1 0 0
    -------------------------
    result : 0 0 0 0  0 0 1 1


## Logical Operation Cookbook

Why would you use these logical operations? On the one hand, these are
the basic opertions that a computer can perform internally on electrical
signals passing through transistor. You can use these operations for
some mathematical trickery, e.g. shifting by one is the same as
multiplying or dividing by two (try it ...).

But for our typical purposes we would like to be able to set the values of
individual bits and to check how individual bits are set. Below are
common patterns to do this, we won't elaborate on them, try these out
yourself

### Setting a bit (to 1)

    value = value | ( 1 << bit_num)


    Set bit 4

    bit num: 7 6 5 4  3 2 1 0
    -------------------------
    byte 1 : 0 0 0 0  1 1 0 0
    1<<4   : 0 0 0 1  0 0 0 0
    -------------------------
    result : 0 0 0 1  1 1 0 0


### Clearing a bit (set to 0)

    value = value | ~( 1 << bit_num)


    Set bit 4

    bit num: 7 6 5 4  3 2 1 0
    -------------------------
    byte 1 : 0 0 0 1  1 1 0 0
    ~(1<<4): 1 1 1 0  1 1 1 1
    -------------------------
    result : 0 0 0 0  1 1 0 0


### Check if a bit is set

    is_set = (1 << bit_num) == (value & (1 << bit_num))

## Hexdecimal

Using 10 digits (0 ... 9) to represent numbers is a fairly arbitray
choice that probably stems from the fact that humans typically have 10
fingers. As you could see above, we can also use two digits (0 and 1) to
count just fine. But we can also use more that 10 digits. This is where
hexadecimal comes in, which uses 16 digits (0, 1, ... 9, A, B, C, D, E,
F) to represent the numbers from 0 to 15. (F = 15).

    | 0 | 0 | 0000 |
    | 1 | 1 | 0001 |
    | 2 | 2 | 0010 |
    |  ...  | ...  |
    | 8 | 8 | 1000 |
    | 9 | 9 | 1001 |
    | A | 10| 1010 |
    | B | 11| 1011 |
    | C | 12| 1100 |
    | D | 13| 1101 |
    | E | 14| 1110 |
    | F | 15| 1111 |

But why on earth would you not want to use a 10 based system to count?
As you can see in the table above, each hexidecimal digit corresponds to
one 4 bit value. And in the previous section you probably had to do a
bit of mental math to figure out what the value of `1000 1000` is.
Hexadecimal make this a lot easier: since `1000` is hexdecimal '8', the
byte `1000 1000` is equal to `0x88`. the "0x" in front of the numbers is
used to indicate that we're dealing with hex.

Once you get used to it, you begin to recognize some patterns: F is all
four bits set, 8 only the first 7 all but the leftmost bit, 1 only the
rightmost bit, etc.

    Top 7 hexadecimal digits to remember:
    | 0 | 0000|
    | 1 | 0001|
    | 2 | 0010|
    | 4 | 0100|
    | 7 | 0111|
    | 8 | 1000|
    | F | 1111|


