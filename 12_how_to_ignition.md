Ignition sense and timed wake-up
 

Ignition sense and timed wake-up are functions which are only available in the SDK on the CloudGate LTE from fw 1.43.0 onwards.

 

Ignition sense
 

The ignition sense feature on the CloudGate allows you to power on the CloudGate by pulling high the iginition sense input line on the power connector of the CloudGate.

 

The input range for the Ignition Sense pin is 0-33V
Signal levels:
VILmax = 2.7V
VIHmin = 4.1V
In words: the input signal will be read as a ‘0’ when the level is lower than 2.7V and will be read as a ‘1’ when the level is higher than 4.1V.
Levels in between are undefined/unpredictable.

 

Pinout

The following drawing shows the pinout of the power connector, seen from the terminal side.

 



 

 

 Pin #	 Function
 1	 Input voltage
 2	 GND
 3	 Ignition sense input
 4	 Not connected
 

 

How it works:

First the Cloudgate boots up by applying the input voltage (9-33Vdc) to it. Once the CloudGate is running it can be shutdown with a call in the SDK. (cg_status_t cg_system_power_down)

When the CloudGate is powered down with this function you will be able to boot it again by pulling high the ignition sense line.

 

This 3rd pin on the power connector can be seen as GPIO 263 in the CloudGate. When you enable an interupt on this GPIO you can detect when it goes down and start the system power_down_call.

You can find and example in the SDK: libcg-examples/src/example_power_down.c

 

Timed wake up
 

The timed wake-up feature allows you to restart the CloudGate after a certain time.

Two settings are available in the SDK to configure the "Timed wake-up" feature:

cg_status_t_ cg system_set_timed_wakeup
CG_status_t cg_system_power_down
 

How it works:

 

First you have to define after how much time the Cloudgate should reboot. (cg_status_t_ cg system_set_timed_wakeup)
Secondly you should shutdown the CloudGate by using: CG_status_t cg_system_power_down
 

LuvitRED
In LuvitRED these features can be used by using the 'CloudGate Control' node. This node accepts messages which control the CloudGate system.

If the payload contains a table with a key command set to 'powerdown'. The powerdown command table should also contain a key option set to one of:

none
ignition
timedwait
igntwait
If set to ignition the ignition sense pin on the CloudGate will be armed to automatically wake the CloudGate up. If set to timedwait or igntwait the waittime before restarting can be specified.

 

example payload:

msg.payload = {

    command = powerdown,
    option = ignition

}

 

 
