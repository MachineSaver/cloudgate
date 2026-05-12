External packages
Since the CloudGate software is based on the OpenWRT stack, the external feed packages provided by OpenWRT are also available to the SDK developer. They can be compiled & installed in the SDK image.

 

The following restriction apply: the compiled packages will be installed in the SDK image path, not at the root directory. They will mounted at /rom/mnt/cust and not at ‘/’, which a normal package would expect. If the package expects libraries, executables, config files to be at a certain absolute path, this will most likely not be correct.

 

Note:
Starting from Option firmware release 1.7.0, the executable search path will include the executable directories of the SDK image: /rom/mnt/cust/bin, /rom/mnt/cust/usr/bin, /rom/mnt/cust/sbin, /rom/mnt/cust/usr/sbin. The loader will also be adjusted to search for libraries in /rom/mnt/cust/lib & /rom/mnt/cust/usr/lib. This will take care of a lot of path dependencies.

 

Concerning package feeds, there is some documentation available on the OpenWRT wiki: see http://wiki.openwrt.org/doc/devel/feeds.

 

Initially, the package feeds will need to be updated. In the SDK root directory, run

 

scripts/feeds update

 

This will fetch all the package definitions in the feeds. After this you can search through the feeds & install/uninstall packages. Eg, you can search for python packages in the feeds by doing

 

scripts/feeds search py

This will return a list of packages you can choose from. To install a package, for example the package ‘python’, do

 

scripts/feeds install python

 

This will install the package and necessary dependencies. Next step is to enable the package for compilation. If you run ‘make menuconfig’ again, you’ll notice that a new section ‘Languages’ appeared.  Diving into this section, you can enable python.
Running ‘make’ now should build python & include it into the developer image.

 

Library path workaround:
For executables unable to load due to library path dependencies, there is a workaround by setting environment variable LD_LIBRARY_PATH to the location of the libraries that need to be loaded.
For example:
export LD_LIBRARY_PATH=/rom/mnt/cust/lib:/rom/mnt/cust/usr/lib
