Use the developers board
Next is an explanation on how to use the different functions on the Option developers board. (CG1105)

 

Click here for the GPIO functionality
Click here for the SPI functionality
Click here for the UART functionality
 

Each functionality can be set via the command line . In case you would like to have the correct configuration after booting the CloudGate you will have to store it in the configuration file called "devices".

 

This file can be changed via uci commands or by uploading the file via the developers image.

 

To change the "devices" file with the use of UCI commands you can use the next commands:

"uci export devices" this gives the values of all parameters stored in the "devices" file.
"uci show devices" this gives the correct syntax of all parameters.
"uci set devices.CG1105.sh_0=echo \"1\" > /sys/bus/platform/drivers/mxs-duart/disable_duart" this disables the debug uart
"uci get device.CG1105.hwv" reads out the hardware version of expansion board 1105
"uci commit" stores all the changed parameters
 

To change the "devices" file with the developers image:

Add the configuration file or a subset of the file in the "config" folder in the main directory of your SDK.

 

Default settings:
By default the "devices" file will contain the next parameters for the developers board:

 

Disable debug uart.
Load the RS232 driver.
Enable MMC2 necessary for the SD card slot.
Configure the relay (gpio 113) as output. You can enable/disable it by writing 1/0 to /dev/relay.
Load the ADC driver for the temperature sensor. You can read the sensor value from /dev/temp_sensor (or /dev/in_voltage3_raw).
Configure the optocoupler (gpio 112) as input. You can read the optocoupler value from /dev/optocoupler.
Configure microUSB as OTG client (mass storage device).
Configure devices:
UART0
ADC (temperature sensor)
Relay
Optocoupler
 

These settings are done by adding the next lines into the "devices" file:

 

config board CG1105

option model "CG1105"
option description "CloudGate Slot 1 developer card"
option slot_id "1"
option hwv "1.0"
option sh_count "14"
option sh_0 "echo \"1\" > /sys/bus/platform/drivers/mxs-duart/disable_duart"
option sh_1 "/sbin/insmod /lib/modules/2.6.35.3/mxs-auart.ko auart_mask=1 uart0_COMM_TYPE=1 uart0_RTSCTS=1 uart0_DTR=118 uart0_DCD=119 uart0_DSR=117 uart0_RI=116"
option sh_2 "echo \"1\" > /sys/module/mxs_mmc/parameters/mmc2_enable"
option sh_3 "echo 113 > /sys/class/gpio/export"
option sh_4 "echo \"out\" > /sys/class/gpio/gpio113/direction"
option sh_5 "echo 0 > /sys/class/gpio/gpio113/value"
option sh_6 "echo 112 > /sys/class/gpio/export"
option sh_7 "echo \"in\" > /sys/class/gpio/gpio112/direction"
option sh_8 "/sbin/insmod /lib/modules/2.6.35.3/imx28-lradc.ko"
option sh_9 "ln -s /sys/devices/platform/mxs-lradc.0/iio:device0/in_voltage3_raw /dev/temp_sensor"
option sh_10 "dd if=/dev/zero of=/tmp/file.dat bs=1M count=5"
option sh_11 "sbin/insmod /lib/modules/2.6.35.3/g_file_storage.ko file=/tmp/file.dat"
option sh_12 "ln -s /sys/class/gpio/gpio113/value /dev/relay"
option sh_13 "ln -s /sys/class/gpio/gpio112/value /dev/optocoupler"
option device_count "4"
option device_0 "uart0"
option device_1 "adc"
option device_2 "relay"
option device_3 "optocoupler"
