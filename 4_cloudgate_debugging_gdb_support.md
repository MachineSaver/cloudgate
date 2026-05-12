Debugging (gdb) support
It is possible to debug a running program either by remote or local gdb. Running gdb on the CloudGate (=local gdb) is not very useful since all executables/libraries are being stripped of debugging information before being put on the CloudGate. Therefore there are no debugging symbols available on the CloudGate. So remote debugging is the most convenient option.

 

Enabling remote gdb
First you need to run ‘make menuconfig’ again in the root of the SDK directory. Go to ‘Utilities’ in the config screen and press enter.

 



 

Press ‘y’ to enable gdbserver and exit the tool.

 

Back on the command-line, type ‘make’ to make your developer image, which will now include gdbserver and then upgrade your CloudGate as detailed in the "Compiling your first image" section.

 

Debugging
After the CloudGate is upgraded, ssh to the CloudGate with the same credentials as on the web UI (usually admin/admin). Change directory to /rom/mnt/cust/usr/bin and launch gdbserver. You can choose which port gdbserver should listen on and which program to debug.

 

In the screenshot below gdbserver will listen on port 7777 and try to debug example_gps, which is one of the SDK examples.

 



 

This will cause the gdbserver to be started and listen on port 7777 for any connections from gdb.

 

Now, on the computer connected to the CloudGate, we need to start gdb and make sure it connects to the gdbserver session.

 

In the scripts directory of the SDK, there is a remote-gdb script which is used to launch gdb sessions to connect to gdbserver. For example:

 

scripts/remote-gdb 192.168.1.1:7777 build_dir/target-arm_v5te_uClibc-0.9.30.3_eabi/libcg-examples-1.0/example_gps

 

This launches gdb and configures it to connect to a host (192.168.1.1 port 7777 in this example) and load symbols for a program (example_gps).

 



 

From this point onwards, you can set breakpoints and step through the program as you would with a normal gdb session.

 

Note: to exit gdbserver type "monitor exit" from your host gdb.
