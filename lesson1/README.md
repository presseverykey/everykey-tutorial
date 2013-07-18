# Setting up the Toolchain

This is the hardest part! So once you're through everything else will
be a breeze. This directory contains instructions for Windows, OSX and
Linux, so pick the correct file and follow along...

## Generic Instructions

You'll need to install `make`, a plattform C compiler, git and the ARM C compiler and bin utilities.

## make

You need `make` to coordinate the compilation of the firmware. It's native to Unix, but Windows needs a little bit of love to install it.

In case you are new to software development, `make` figures dependencies of different steps in a build process, will check which files have changed and will only run the necessary steps, i.e. it won't recompile files that haven't changed.

## Plattform C compiler

This will be `gcc`, the SDK contains a small tool that updates a checksum in the firmware. The source of the tools is
included in our project, but you need to compile it for your plattform (i.e. Windows, Linux, OSX) so you need a compiler for your plattform.

## git

`git` is a very common version control tool. It's used to keep track of changes during the life of a project. Our SDK is hosted on github and you'll require software to download the project.

## ARM C Compiler

The firmware we're compiling isn't meant for your own computer, but will run on the anykey which speaks a "different language", the gcc that you also need is able to translate programs for your PC. We need a compiler that can transform the C code into a language the anykey can understand.

