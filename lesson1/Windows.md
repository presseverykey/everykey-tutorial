# Installing the Anykey Toolchain on Windows.

## Install cygwin ...

You'll need to download cygwin tools in order to have a working copy of
`git`. `make` and a C-compiler that you need to compile a tool to update
the checksum in the generated firmware (don't worry if you don't know
what this means.. :)

Go to [http://cygwin.com/intall.html] and download the `setup.exe`

![](https://raw.github.com/anykey0xde/tutorial/master/img/download_cygwin.png)

Run the installer and make sure you install the following packages:

* git
* make
* gcc
* wget
* unzip

![](https://raw.github.com/anykey0xde/tutorial/master/img/cygwin.gcc.png)

Next start cygwin:

![](https://raw.github.com/anykey0xde/tutorial/master/img/cygwin.start.png)

and check out the anykey-sdk repository:

    git clone https://github.com/anykey0xde/anykey-sdk.git

Switch into the sdk directory, then to the `checksum` subdirectory and run `make`

![](https://raw.github.com/anykey0xde/tutorial/master/img/cygwin.step1.png)


## Install the ARM compiler

Finally, you'll need to install the ARM compiler (the gcc installed was only necessary to compile the `checksum` program.

Head to https://launchpad.net/gcc-arm-embedded/+download find the link to the download called "Windows Installer" Package". Copy that link and download it using `wget`

Run the installer and MAKE SURE you tick the "add path to environment variable" box.

![](https://raw.github.com/anykey0xde/tutorial/master/img/win-arm-installer.png)

## If you're still not fed up with using Windows ...

Ok, now for our dirty secret: we've used symbolic links in our examples and windows isn't too happy with those. Anytime we used symlinks, you'll need to actually copy the linked directory. Here's an exmaple:

Go back into the SDK directory where you checkout out our stuff from there into the `examples/blink` directory. A directory listing will show a lot of symbolic links:

    a2800276@windowsxp ~/anykey-sdk/examples/blink
    $ ls -l
    total 9
    lrwxrwxrwx 1 a2800276 None   12 Jul 16 18:23 anykey -> ../../anykey
    lrwxrwxrwx 1 a2800276 None   26 Jul 16 18:23 anypio.c -> ../../libs/anypio/anypio.c
    lrwxrwxrwx 1 a2800276 None   26 Jul 16 18:23 anypio.h -> ../../libs/anypio/anypio.h  
    lrwxrwxrwx 1 a2800276 None   23 Jul 16 18:23 lpc1343.ld -> ../../anykey/lpc1343.ld
    -rw-r--r-- 1 a2800276 None 3207 Jul 16 18:23 main.c
    lrwxrwxrwx 1 a2800276 None   21 Jul 16 18:23 makefile -> ../../anykey/makefile

delete them and copy the originals in their place:

    $ rm anykey
    $ cp -r ../../anykey .
    $ rm anypio.*
    $ cp ../../libs/anypio.* .
    ...
    
and you're set to go... type `make`

    $ make



