How to use I2C

How to use I2C devices.
 

The CloudGate has two I2C interfaces:

The primary I2C interface = I2C_SYS
The secondary I2C interface = I2C_AUX
 

The primary I2C interface
The primary I2C interface is activated during boot time and will always work. In order to read or write to devices attached to this I2C interface you can use a small program we have made:

rw_i2c_eeprom -a 0x51 -r -f foo -d /dev/i2c-0

The example above will read something from the eeprom with address 0x51 to file “foo”.

The rw_I2C_eeprom tool is based on : https://www.kernel.org/doc/Documentation/i2c/dev-interface

 

Secondary I2C
The secondary I2C interface is not active during boot time. It's used as a debug UART during boot time to make logfile during production.

Before you can use this secondary I2C interface you will first have to disable the debug interface. This can be done with the next command:

echo 1 > /sys/bus/platform/drivers/mxs-duart/disable_duart

Afterwards you can enable the I2C_aux interface with:

echo 1 > /sys/module/i2c_mxs/parameters/i2c1_enable

 

Read and write with this secondary I2C interface is identical to the primary I2C interface:

(except the device name = /dev/i2c-1)

rw_i2c_eeprom -a 0x51 -r -f foo -d /dev/i2c-1
