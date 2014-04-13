# Installing the Everykey Toolchain on Windows.

## Install cygwin ...

You'll need to download cygwin tools in order to have a working copy of
`git`. `make` and a C-compiler that you need to compile a tool to update
the checksum in the generated firmware (don't worry if you don't know
what this means.. :)

Go to [http://cygwin.com/intall.html] and download the `setup.exe`

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/download_cygwin.png)

Run the installer and make sure you install the following packages:

* git
* make
* gcc
* unzip
* ca-certificates

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/cygwin.gcc.png)

Next start cygwin:

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/cygwin.start.png)

and check out the everykey-sdk repository:

    $ git clone https://github.com/presseverykey/everykey-sdk.git

Switch into the sdk directory, then to the `checksum` subdirectory and run `make`

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/cygwin.step1.png)

Finally, you need to add the checksum directory to your `$PATH` variable so it can be found:

    $ cd ~/everykey-sdk/checksum
    $ export PATH=$PATH:`pwd`

## Install the ARM compiler

Finally, you'll need to install the ARM compiler (the gcc installed was only necessary to compile the `checksum` program.

Head to https://launchpad.net/gcc-arm-embedded/+download find the link to the download called "Windows Installer" Package". Copy that link and download it using `wget`

Run the installer and MAKE SURE you tick the "add path to environment variable" box.

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/win-arm-installer.png)

## If you're still not fed up with using Windows ...

Ok, now for our dirty secret: we've used symbolic links in our examples
and windows isn't too happy with those. Anytime we used symlinks, you'll
need to actually copy the linked directory. Here's an exmaple:

Go back into the SDK directory where you checkout out our stuff from
there into the `examples/blink` directory. A directory listing will show
a lot of symbolic links:

    a2800276@windowsxp ~/anykey-sdk/examples/blink
    $ ls -l
    total 9
    lrwxrwxrwx 1 a2800276 None   12 Jul 16 18:23 everykey -> ../../everykey
    lrwxrwxrwx 1 a2800276 None   26 Jul 16 18:23 anypio.c -> ../../libs/anypio/anypio.c
    lrwxrwxrwx 1 a2800276 None   26 Jul 16 18:23 anypio.h -> ../../libs/anypio/anypio.h  
    lrwxrwxrwx 1 a2800276 None   23 Jul 16 18:23 lpc1343.ld -> ../../everykey/lpc1343.ld
    -rw-r--r-- 1 a2800276 None 3207 Jul 16 18:23 main.c
    lrwxrwxrwx 1 a2800276 None   21 Jul 16 18:23 makefile -> ../../everykey/makefile

delete them and copy the originals in their place:

    $ rm everykey
    $ cp -r ../../everykey .
    $ rm anypio.*
    $ cp ../../libs/anypio.* .
    ...

When you're done, the directory should look like this:

    $ ls -l
    total 20
    drwxr-xr-x+ 1 a2800276 None    0 Jul 16 19:34 everykey
    -rw-r--r--  1 a2800276 None 2319 Jul 16 19:00 anypio.c
    -rw-r--r--  1 a2800276 None 2495 Jul 16 19:00 anypio.h
    -rw-r--r--  1 a2800276 None 2183 Jul 16 19:31 lpc1343.ld
    -rw-r--r--  1 a2800276 None 3207 Jul 16 18:23 main.c
    -rw-r--r--  1 a2800276 None 1619 Jul 16 19:34 makefile



and you're set to go... type `make`

    $ make

BTW: We're not happy with all the deleting and copying either to deal with symlinks under Windows and would be happy to accept a solution to deal with this ...


## Check everything works

There should be a file called `firmware.bin` in the blink directory.
This file needs to be copied onto the Everykey. Plug in your Everykey using
a micro-USB cable. You need to reset the Everykey in order to make it show
up as a "USB-Stick" in the Finder. To do this, hold down the PROGRAM
button, press the RESET button and release the PROGRAM button. The LED
should now glow softly and you will see a drive called `CRP DISABLED`
in the Explorer.

![](https://raw.github.com/presseverykey/everykey-tutorial/master/img/reset_prg_buttons.png)


Running `make mac-install` will copy the firmware to the Everykey and
eject it. Now press RESET again to exit programming mode and the LED
should be blinking.

CONGRATULATIONS! the hardest part is behind you...
