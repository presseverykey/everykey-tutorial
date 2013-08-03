# Installing the Anykey Toolchain on Linux.

## make, gcc, git

Chances are, you already have these installed. in case you don't install
them via the package manager your system is using ...


## Install the arm compiler

In case your package manager has a package called something like
`arm-none-eabi-gcc` install that. If there's a `arm-none-eabi-binutils`
install that too. If not,, download the 'Linux installation tarball' package from
[launchpad](https://launchpad.net/gcc-arm-embedded/+download), unpack the the archive in a
convenient location:

    $ mkdir compiler
    $ cd compiler
    $ tar -xjf
    $download_location/gcc-arm-none-eabi-4_7-2013q2-20130614-linux.tar.bz2

and add the `bin` directory to your `$path` variable:

    $ export PATH=$PATH:`pwd`/gcc-arm-none-eabi-4_7-2013q2/bin/

you'll need to edit the $PATH variable everytime you start a new
console, consider adding the path adjustment to your `.profile` ... 

you may need to change the commands above in case you're using a newer
version of the tools and the files aren't called
`gcc-arm-none-eabi-4_7-2013q2-20130614`

## Checkout out the SDK

Create a convenient working directory and check out the SDK

     $ mkdir anykey; cd anykey
     $ git clone https://github.com/anykey0xde/anykey-sdk.git

## make checksum and add it to your PATH

Just one more thing... We need a small utility to adjust the firmware we
compile for the LPC1343. Change into the `checksum` directory and run
make:

    $ cd anykey/anykey-sdk/checksum
    $ make

 Make sure the executable works, it should issue an error:

     $ ./checksum
     syntax: checksum <firmware.bin>

 Finally, add it to your PATH

     $ export PATH=$PATH:`pwd`

## Check everything works

Change into the `examples/blink` directory. Blinking LEDs are the
HelloWorld of embedded programming! You should just be able to run
`make` and the firmware should be created.


CONGRATULATIONS! the hardest part is behind you...
