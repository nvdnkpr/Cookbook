# Compiling ArangoDB under Windows

## Problem

I want to compile ArangoDB under Windows.

**Note:** For this recipe you need at least Arango 2.4. For Arango version before 2.4 look at the [old Compiling ArangoDB under Windows](https://docs.arangodb.com/CompilingUnderWindowsLegacy.md).

## Solution

While compiling ArangoDB under Linux is straight forward - simply execute `configure` and `make` - compiling under Windows 
is a bit more demanding.

### Ingredients

For this receipt, you need to install the following programs under Windows:

* [cygwin](https://www.cygwin.com/)

You need at least `make` from cygwin. Cygwin also offers a `cmake` do not install this version. 

* [cmake](http://www.cmake.org/)

Either version 2.8.12 or 3.0.2 should work. Make sure to download the 64bit version.

* Visual Studio Express 2013 for Windows Desktop

Please note that there are different versions of Visual Studio. The `Visual Studio for Windows` will **not work**. You need to
install `Visual Studio (Express) for Windows Desktop`. You must configure your path in such a way that the compiler can
be found. One way is to execute the `vcvarsall.bat` script from the `VC` folder.

* [Nullsoft Scriptable Install System](http://nsis.sourceforge.net/Download)

### Building the required libraries

First of all, start a `bash` from cygwin and clone the repository using the corresponding branch, e.g. for ArangoDB 2.4:

    git clone -b 2.4 https://github.com/arangodb/arangodb-windows-libraries.git

and switch into the directory `arangodb-windows-libraries`. This repository contains the open-source libraries which
are needed by ArangoDB:

* etcd from CoreOS
* getopt
* libev
* linenoise
* openssl
* regex
* zlib

In order to build the corresponding 32bit and 64bit versions of these libraries, execute

    make
    make install
    make 3rdParty

This will create a folder `WindowsLibraries` containing the headers and libraries.

### Building ArangoDB itself

Clone the repository

    https://github.com/triAGENS/ArangoDB.git

and copy the previously created `WindowsLibraries` folder into this directory `ArangoDB`.

Next you need to build Google's V8 version, that is part of ArangoDB. This version contains V8 and ICU. Switch into the directory

    ArangoDB/3rdParty

and execute

    make -f Makefile.v8-windows
    make -f Makefile.v8-windows install


Now switch back into the `ArangoDB` root folder and execute

    make pack-win32

or

    make pack-win64

in order to build the installer file for either 32 bit or 64 bit.

### Executables only

If you do not need the installer file, you can use the `cmake` to build the executables. Instead of `make pack-win32`
use

    mkdir Build32
    cd Build32
    cmake -G "Visual Studio 12" ..

This will create a solution file in the `Build32` folder. You can now start Visual Studio and open this
solution file.

To build the 64 bit version, use

    mkdir Build64
    cd Build64
    cmake -G "Visual Studio 12 Win64" ..

Alternatively use `cmake` to build the executables.

    cmake --build . --config Release

In order to execute the binaries you need to copy the ICU datafile into the directory containing the
executable

    cp WindowsLibraries/64/icudtl.dat Build64/bin/Debug/icudt52l.dat

**Author**: [Frank Celler](https://github.com/fceller)

**Tags**: #windows
