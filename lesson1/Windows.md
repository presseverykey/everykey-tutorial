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

Next start cygwin:

![](https://raw.github.com/anykey0xde/tutorial/master/img/cygwin.start.png)

and check out the anykey-sdk repository:

    git clone https://github.com/anykey0xde/anykey-sdk.git

Switch into the sdk directory, then to the `checksum` subdirectory and run `make`

![](https://raw.github.com/anykey0xde/tutorial/master/img/cygwin.step1.png)
