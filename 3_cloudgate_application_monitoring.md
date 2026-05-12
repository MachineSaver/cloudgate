Application monitoring
So far, none of the examples shown will run automatically. This is not desirable in most scenario’s as you really want the CloudGate to actually run the application once it boots up.

There are 2 ways to do this as shown in the following sections

ISV executable
This is the first method. When booted up, the CloudGate will search for an executable called ‘isv’ in the root (/rom/mnt/cust) of the developer image. If not present, it won’t complain and just continue running. If present, it will run it. However, this is a ‘fire and forget’ launch: if the executable exits or crashes, it will never be restarted. Neither is this event logged. Nevertheless, this might be well suited to some applications.

If you want to try this, modify one of the examples mentioned in the  'Compiling Your First Image' tutorial page. Change an executable name in package/libcg-examples/src/Makefile to ‘isv’ and adjust package/libcg-examples/Makefile to make sure ‘isv’ gets installed in the root of the SDK image (instead of /bin). If you then flash your developer image to the device and reboot, you should see that the CloudGate has started the application.

‘clients’ config entry
The CloudGate uses a watchdog daemon to start CloudGate processes, monitor them and check if they are still alive. If a monitored process exits, this will be logged and the process will be restarted. Applications developed using the SDK can also make use of this facility. All that is needed is creating a file called ‘clients’ in the config/config subdirectory of the SDK. In this entry you create an entry for each process that needs to be monitored. For example,

config client MyApp
        option name "MyApp "
        option type "customer_app" 

When creating & flashing the developer image, this configuration entry will be integrated in the configuration of the watchdog process and your MyApp process will be started automatically. It will also be restarted if it exits or crashes.

The default paths where the watchdog process will search for the application are /rom/mnt/cust/bin, /rom/mnt/cust/usr/bin, /rom/mnt/cust/sbin & /rom/mnt/cust/usr/sbin. If your application is present in any of these locations, you only need to specify the name. If this is not the case, you’ll need to specify the location of your application relative to any of these paths, for example

config client MyApp
        option name "../../MyApp "
        option type "customer_app"
