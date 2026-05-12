SDK: Getting started
The router software is based on OpenWRT, which has a feature rich build system based on buildroot.
The SDK uses a similar build-system. In fact, the SDK is generated straight from Option’s router software with a few modifications done during this process.

There is a 32-bit and 64-bit version of the SDK available. In order to build the SDK you will need an Intel PC running a Linux distribution with at least the following tools installed:

make, getopt, fileutils, gcc, ncurses, zlib, awk, flex, unzip, bzip2, xz, patch, perl, python (version 2), luajit, wget, tar, find, ccache, cmake, libtool.

The SDK configuration tool will warn you if the necessary dependencies are missing. Note that in many cases the developer versions of these packages are required.

If you want to cross-compile luajit from a 64-bit host, you need to install the multilib development package as well: 'sudo apt-get install libc6-dev-i386'


Note:
Officially supported distributions are Ubuntu LTS versions 18 and 20. The SDK requires python version 2.7. You can use Docker or pyenv to install the correct version.


When you unpack the SDK, you will see the following directories:

‘include’: buildsystem scripts related to OpenWRT/buildroot.
‘keys’:  contains the developer key for signing the resulting image
‘config’: contains all configuration files that can be bundled with the resulting image.
‘package’:  this is where development will take place & you will create your own applications.
‘scripts’:  OpenWRT-related scripts
‘target’:  contains various platform specific settings.
‘staging_dir’:  this directory contains several things:
Binaries of host tools: toolchain + various host-tools that are necessary to create an image.
Binaries of compiled packages
Header files that are needed for some packages to compile. For example, say package A depends on header files of package B: package B will compile first, put the header files in the staging dir. After that, package A can compile.
The resulting, assembled filesystem.
 

Proceed with the "Compiling your first image" guide to get started.
