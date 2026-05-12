Configure the GPIO's
 

First you will need to define the offset of the GPIO pin. It is calculated as follows:

 

example: GPIO3_4 => offset=(3x32)+4=100

 

Sysfs is used to configure the gpio’s. Sysfs is a virtual file system provided by the Linux kernel. Sysfs exports information about various subsystems, hardware devices and drivers to user space. The Linux provided gpiolib sysfs entries can be found at  /sys/class/gpio.

The next commands are available:

 

Export:    export a gpio to claim it. Once exported, the application has exclusive usage of the gpio. When the application does not need the gpio any more, it must unexport the gpio to allow other applications to claim the gpio.

 

Unexport: unexport a gpio to release it. Once unexported, other applications can claim exclusive usage of the gpio.

 

Direction: reads as either “in” or “out”. Defines the direction of the gpio. For digital outputs, it must be set to “out”. For digital inputs, it must be set to “in”.   

 

Value: reads as either 0 (low) or 1 (high). If the gpio is configured as an ouput, this value may be written.

 

Edge: reads as either “none”, “rising” or “falling”. Write these strings to select the signal edge(s) to generate interrupts on.

 

Active_low: reads as either 0 (false) or 1 (true). Set this to “true” (or to any nonzero value) to invert the “Value”  attribute both for reading and writing.

 

To set a GPIO (GPIO3_3 = offset 99) as output write the next commands:

 

echo 99 > /sys/class/gpio/export
echo "out" > /sys/class/gpio/gpio99/direction
echo 1 > /sys/class/gpio/gpio99/value

 

("echo 1" makes the output high, "echo 0" makes the output low!)

 

Example:

 

To set a GPIO (GPIO3_16 = offset 112) as input write the next commands:

 

echo 112 > /sys/class/gpio/export
echo "in" > /sys/class/gpio/gpio112/direction
cat  /sys/class/gpio/gpio112/value

 

For more information you can have a look at the next link:

https://www.kernel.org/doc/Documentation/gpio/sysfs.txt

http://developer.ridgerun.com/wiki/index.php?title=Gpio-int-test.c

 

GPIO interrupts
 

Interrupts are enabled on input gpio’s and catched via a blocking poll/select on the gpio. When interrupts are enabled, they are generated based on a transition of the input (edge) (level based interrupts are not supported). To enable edge based interrupt generation, write “rising” or “falling” to the edge gpiolib sysfs entry of a gpio. Only one interrupt may be configured per gpio. Writing "none” to the edge sysfs entry, disables all interrupts.

 

Example for edge based interrupts:
 

Export the gpio (assume GPIO 3_4 = GPIO 100):
echo 100 > /sys/class/gpio/export

 

Enable edge interrupt for gpio 100 (e.g. falling edge):
echo falling > /sys/class/gpio/gpio100/edge

 

Disable edge interrupt for gpio 100:
echo none > /sys/class/gpio/gpio100/edge

 
