How to Make Developer Image Persistent
Source: https://cloudgateuniverse.com/docs/make-your-image-persistent

A developers image can be made persistent so that it will remain on the CloudGate even after a factory reset.

Two methods of image persistence are possible:

Hard Persistence
An image which is hard persistent will not be erasable by a factory reset. When a bug is found in the image running on the CloudGate you will not be able to remove the image!

Steps to make an image hard persistent:
1. Set the "hard persistence" attribute during the make process.
2. The image will have to be signed by Option. Once Option delivers you the signed image you will be able to use it.
   Please mail your request to cgdeveloper@option.com.

Soft Persistence
An image which is "soft persistent" will not be removed after a factory reset. However, when you press the reset button for more than 55 seconds the CloudGate will reboot but will not start the image. This gives you the chance to upload a new image to the CloudGate.

Steps to make an image soft persistent:
1. Make the file with the "soft persistent" attribute enabled.
2. The image still has to be signed by Option before you can use it.
   Please mail your request to cgdeveloper@option.com.

Firmware Version Range
When making an image soft or hard persistent you will have to add a minimum and maximum firmware version. Once these versions are set you will not be able to upgrade the Option firmware outside the given firmware version range.
