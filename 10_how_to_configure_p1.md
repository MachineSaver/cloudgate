P1 interface
Device : /dev/i2c-1

P1 interface control commands
p1_interface_ctrl value	cortex value	function
hidden	-	00: No status change
1	0x40	Enable P1 interface. switch on the power of the E-meter P1 interface and configure UART1 Rx pin of the Cortex.
2	0x80	Inhibit P1 Interface: switch off the power of the E-meter P1 interface. This inhibits future data transfer.
3	0xC0	Shutdown P1 Interface: switch off the power of the E-meter P1 interface and UART1 Rx pin of the Cortex is reprogrammed as GPIO.
P1 mode commands
Disable DSMR parsing and switch to raw mode:
echo 1 > /sys/devices/virtual/meterio/cortex/p1_raw_mode


Enable DSMR parsing (default)
echo 0 > /sys/devices/virtual/meterio/cortex/p1_raw_mode

