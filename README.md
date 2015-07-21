Parse Embedded C SDKs (for Intel IoT Developer Kit)
===================================================

These instructions will provide you with a Parse Embedded C SDKs provide
support for IOTDK platforms such as Intel Galileo, Intel Edison and Intel
Minnowboard Max.

## Getting started

We provide a binary package for the Parse SDK if you have an IOTDK 1.5 image.
If your board is not yet configured follow the [Getting Started](https://software.intel.com/en-us/iot/library/edison-getting-started) guide
to install the latest image and get online. You can also build and run the Parse
samples with [Eclipse](https://software.intel.com/en-us/iot/downloads#ide).
Find out more about how to get started with Eclipse [here](https://software.intel.com/en-us/eclipse-getting-started-guide).

First, add the IoTDK repository to the opkg configuration files:

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

This will also resolve and install any dependencies for the Parse SDK onto your board.

## Recompiling the parse SDK

The parse SDK uses autotools, here is the 10 second method for building it.

~~~~~~~~~~~~~{.sh}
autoreconf -vif
./configure --prefix=/usr
make
make install
~~~~~~~~~~~~~

This is useful if you updated the sources to the latest version from the Parse SDK
[repository](https://github.com/ParsePlatform/parse-embedded-sdks).

## Saving a Parse Object

Download the unix sample code and makefile from [here](https://github.com/ParsePlatform/parse-embedded-sdks/tree/master/unix/samples/raspberry-pi-starter-project).
If you're using Eclipse, use the makefile to configure the project settings.

Add the following code to the *main* function in *main.c* and replace `ApplicationID` and `ClientKey`
with your values:

~~~~~~~~~~~~~{.c}
ParseClient client = parseInitialize("ApplicationID", "ClientKey");
parseSendRequest(client, "POST", "/1/classes/TestObject", "{\"foo\":\"bar\"}", NULL);
~~~~~~~~~~~~~

Save the changes and copy the files to the board. Type the following commands in a terminal
to build and run:

~~~~~~~~~~~~~{.sh}
make
./quickstart
~~~~~~~~~~~~~

A new `TestObject` with field `foo` and value `bar` will be created and sent to the Parse Cloud.

## Receiving a Push Notification

Add the following function above your *main* function in *main.c*. This will add a handler to
respond to a push notification.

~~~~~~~~~~~~~{.c}
void myPushCallback(ParseClient client, int error, const char *buffer) {
  if (error == 0 && buffer != NULL) {
    printf("received push: '%s'\n", buffer);
  }
}
~~~~~~~~~~~~~

Next, add the following lines inside *main* function of the *main.c* file. This will register the handler you just added, initialize the push listener service, and begin listening for pushes.

~~~~~~~~~~~~~{.c}
parseSetPushCallback(client, myPushCallback);
parseStartPushService(client);
parseRunPushLoop(client);
~~~~~~~~~~~~~

Build and run your app again. To test push receiving, send a push using the following *curl* command:

~~~~~~~~~~~~~{.sh}
curl -X POST \
  -H "X-Parse-Application-Id: YOUR_PARSE_APPLICATION_ID" \
  -H "X-Parse-REST-API-Key: YOUR_PARSE_REST_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "where": {
          "deviceType": "embedded"
        },
        "data": {
          "alert": "A test push from Parse!"
        }
      }' \
  https://api.parse.com/1/push
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

