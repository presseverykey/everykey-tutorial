# Installing the Everykey Toolchain on OSX.

## make, gcc

Chances are, you already have these installed. Simply start your terminal and type in gcc; this should tell you your gcc-version if its already there. Also type in make – in case you have Xcode installed this would give you something like: No targets specified and no makefile found.  Stop.
In case you don't there are a number of options:

1. install XCode via the AppStore
2. use macports:
  * `port` packages are named `gmake` and `gcc48`

_Editors Note: `homebrew` doesn't seem to support either gnu make or gcc ... go figure_


## git

In case you don't have git installed, follow the instructions [here](http://git-scm.com/book/en/Getting-Started-Installing-Git)

## Install the ARM compiler

Either use macports:

    $ port install arm-none-eabi-gcc

Alternatively, download the mac packages from
[launchpad](https://launchpad.net/gcc-arm-embedded/+download) (homebrew
doesn't support the arm compiler either...), unpack the the archive in a
convenient location:

    $ mkdir compiler
    $ cd compiler
    $ tar -xjf $DOWNLOAD_LOCATION/gcc-arm-none-eabi-4_7-2013q2-20130614-mac.tar.bz2

and add the `bin` directory to your `$PATH` variable:

    $ export PATH=$PATH:`pwd`/gcc-arm-none-eabi-4_7-2013q2/arm-none-eabi/bin/

You'll need to edit the $PATH variable everytime you start a new
console, consider adding the PATH adjustment to your `.profile`

You may need to change the commands above in case you're using a newer
version of the tools and the files aren't called
`gcc-arm-none-eabi-4_7-2013q2-20130614`

### Further Options

#### Homebrew

There is no official homebrew formula for `arm-none-eabi-gcc`, but you can use the following tap to install the precompiled binaries:

    $ brew tap superquadratic/anykey
    $ brew install arm-none-eabi-gcc

#### DIY
If you prefer to spend an hour compiling everything from the sources, there's a quick rundown [here](http://blog.y3xz.com/blog/2012/10/07/setting-up-an-arm-eabi-toolchain-on-mac-os-x/).


## Check out out the SDK

Create a convenient working directory and check out the SDK

    $ cd pathToYourFolder
    $ mkdir choose a Name, e.g. everykey
    $ cd everykey
    $ git clone https://github.com/anykey0xde/anykey-sdk.git


## make checksum and add it to your PATH

Just one more thing... We need a small utility to adjust the firmware we
compile for the LPC1343. Change into the `checksum` directory and run
make:

    $ cd everykey/anykey-sdk/checksum
    $ make

 Make sure the executable works, it should issue an error:

     $ ./checksum
     syntax: checksum <firmware.bin>

 Finally, add it to your PATH

     $ export PATH=$PATH:`pwd`

## Check if everything works

Change into the `examples/blink` directory. Blinking LEDs are the
HelloWorld of embedded programming! You should just be able to run
`make` and the firmware should be created.

There should be a file called `firmware.bin` in the blink directory.
This file needs to be copied onto the Everykey. Plug in your Everykey using
a micro-USB cable. You need to reset the Everykey in order to make it show
up as a "USB-Stick" in the Finder. To do this, hold down the PROGRAM
button, press the RESET button, release it and then release the PROGRAM button. The LED
should now glow softly and you will see a drive called `CRP DISABLED`
in the Finder.

![](https://raw.github.com/anykey0xde/tutorial/master/img/reset_prg_buttons.png)


Running `make mac-install` will copy the firmware to the Everykey and
unmount it. Now press RESET again to exit programming mode and the LED
should be blinking.

CONGRATULATIONS! the hardest part is behind you...
