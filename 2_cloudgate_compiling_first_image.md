Compiling your first image
In the root of the SDK, type ‘make menuconfig’.

If your build environment is still missing some dependencies and the OpenWRT Configuration page does not open, please install the following dependencies with this command:                                                                                                                                                                      sudo apt-get install build-essential gawk zlib1g-dev git libncurses5-dev subversion lzop gengetopt flex zip autoconf ccache cmake libtool

This should give you the following menu:
<./image-1>
 

“Target Images” contains the configuration for image signing. Do not change any settings here.

"Image Attribute Settings" is used to set soft or hard persistence. Please reach out to developer@option.com to sign your application.

“Image configuration” is not used. Do not change any settings here.

The other directories contain various programs, utilities & libraries that can be enabled. Some are enabled by default: this usually concerns tools which are included in the Option firmware and need to be enabled in order to satisfy dependencies for other packages that can be included (eg, from the OpenWrt repositories).

So let’s enable the SDK examples: in “Base System”, press ‘y’ to include the “libcg-examples” package in the build.

Now exit the menu configuration, make sure to say ‘Yes’ when the tool asks you to save the configuration.

Before we can proceed with compiling your first image, we need to install some packages that are necessary for the CloudGate examples library to compile sucessfully.

Enter the following commands in the root of the SDK. Please make sure your computer is connected to the internet. This update can take some minutes to complete.

./scripts/feeds update

./scripts/feeds install libsqlite3


Once the installation has finished, the image should be ready to build. Enter ‘make’ in the root of the SDK. You should see the following output:

The SDK has compiled an image successfully! If something might go wrong, please run the 'make' command again with 'V=99' behind it. This will print out all compile steps.

In ‘bin/imx28’, you can find the bundle-sdk-xxx.zip zip file. It contains both the compiled code and the config files. The bundle-sdk-xxx.zip file can be uploaded to the CloudGate Universe 'Provisioning' tab or via the local web interface on the CloudGate itself. Please find the latter explained below. 

Log in to the CloudGate web interface (accessible via 192.168.1.1 in your web browser) and click on the ‘Provisioning’ tab. In the section ‘Upload Option provisioning file’, you can navigate to the bundle-sdk-xxx.zip file and click upload (see screenshot below).

<image-2> 

You should see a screen asking you if you want to restart. Click ‘Restart’ to restart the CloudGate.

After the restart the CloudGate will have verified & mounted the SDK image. Now ssh into the CloudGate and the SDK image will be mounted in /rom/mnt/cust. Change directory to /rom/mnt/cust/bin and there you will see the compiled example which you can try to run.

None of these executables will run automatically however. In order to make it run automatically, you need to change an executable name in package/libcg-examples/src/Makefile to ‘isv’ and adjust package/libcg-examples/Makefile to make sure ‘isv’ gets installed in the root of the SDK image (instead of /bin).

Should you want to create your own application, have a look at libcg-examples in the packages directory. You can use it as template for your own application. Just remember to call the executable ‘isv’ if you want to run it automatically when the CloudGate boots.

Important:
Make sure that you have disabled the "automatic provisioning" feature in the WebGUI of the CloudGate before you upload your own image. Otherwise the CloudGate will overwrite your image again when it checks for an update on the CloudGate Universe.
