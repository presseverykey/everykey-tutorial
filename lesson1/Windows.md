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

Make a directory for the compiler:

    $ mkdir compiler
    $ cd compiler

Head to https://launchpad.net/gcc-arm-embedded/+download find the link to the download called "Windows Zip Package". Copy that link and download it using `wget`

    $ wget --no-check-certificate https://launchpad.net/gcc-arm-embedded/4.7/4.7-2013-q2-update/+download/gcc-arm-none-eabi-4_7-2013q2-20130614-win32.zip
    
Unzip the downloaded package and add the compiler to your `PATH`, make the programs executable, and make sure they'll run:

    $ unzip gcc-arm-none-eabi-4_7-2013q2-20130614-win32.zip
    $ export PATH=$PATH:`pwd`/GNU\ Tools\ ARM\ Embedded/4.7\ 2013q2/bin
    $ chmod +x GNU\ Tools\ ARM\ Embedded/4.7\ 2013q2/bin
    $ arm-none-eabi-gcc
    
And you should get an error message saying you didn't provide an input file.
