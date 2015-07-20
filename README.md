Parse Embedded C SDKs (for Intel IoT Developer Kit)
===================================================

These instructions will provide you with a Parse Embedded C SDKs provide
support for IOTDK platforms such as Intel Galileo, Intel Edison and Intel
Minnowboard Max.

## Getting started

We provide a binary package for the parse SDK if you have an IOTDK 1.5 image.
If you have an edison you need to first add the repository:

~~~~~~~~~~~~~{.sh}
echo "src iotdk-all http://iotdk.intel.com/repos/1.5/iotdk/all" >> /etc/opkg/iotkit.conf
echo "src iotdk-i586 http://iotdk.intel.com/repos/1.5/iotdk/core2-32" >> /etc/opkg/iotkit.conf
echo "src iotdk-quark http://iotdk.intel.com/repos/1.5/iotdk/core2-32-intel-common" >> /etc/opkg/iotkit.conf
echo "src iotdk-x86 http://iotdk.intel.com/repos/1.5/iotdk/intel_core2_32" >> /etc/opkg/iotkit.conf
~~~~~~~~~~~~~

Once the repository is added and your board is online update the package list and install

~~~~~~~~~~~~~{.sh}
opkg update
opkg install libparse-dev
~~~~~~~~~~~~~

## Recompiling the parse SDK

The parse SDK uses autotools, here is the 10 second method for building it.

~~~~~~~~~~~~~{.sh}
autoreconf -vif
./configure --prefix=/usr
make
make install
~~~~~~~~~~~~~

### FAQ

Q. Where do I get help?
A. If it's Intel platform specific, file an issue on this github repository or
use our maker community forum https://communities.intel.com/community/makers.
If it's about the Parse SDK then use the traditional Parse support methods.

Q. Why is there no code?
A. We don't make any code changes to the Parse SDK, get the sources from
https://github.com/ParsePlatform/parse-embedded-sdks

Q. How do I rebuild your ipk package?
A. Folow the instructions here for building the IOTDK image
https://software.intel.com/en-us/blogs/2015/03/04/creating-a-yocto-image-for-the-intel-galileo-board-using-split-layers.
You will be able to build the package 'parse-embedded-sdks'. The bitbake recipe
is here:
http://git.yoctoproject.org/cgit/cgit.cgi/meta-intel-iot-middleware/tree/recipes-devtools/parse-embedded-sdks/parse-embedded-sdks_1.0.1.bb?h=dizzy

