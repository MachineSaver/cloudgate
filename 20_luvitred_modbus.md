LuvitRED Modbus Nodes
Source: https://cloudgateuniverse.com/docs/nodes

This document covers all Modbus-related nodes available in LuvitRED for CloudGate.
For PDF tutorials see: https://cloudgateuniverse.com/docs/tutorials

---

modbus control
Configure Modbus requester/responder settings.
To update settings, set msg.payload to a table containing the new configuration.
If an argument is omitted, the current setting is retained.

Serial Settings Example:
msg.payload = {
  enabled = true/false,
  port = '/dev/ttySP0',
  baud = 19200,
  databits = 8,
  parity = 'none',  -- Options: 'none', 'even', 'odd', 'mark', 'space'
  stopbits = 1,
  mode = 'rs232',   -- Options: 'rs232', 'rs485', 'rs485term', 'rs485duplex', 'rs485duplexterm'
  slaveid = 1,
  timeout = 1000,   -- Timeout in milliseconds
  retries = 3,
  log = true        -- Enable logging
}

TCP Settings Example:
msg.payload = {
  enabled = true/false,
  host = '192.168.1.1',
  port = 502,
  timeout = 1000,   -- Timeout in milliseconds
  retries = 3,
  log = true        -- Enable logging
}

---

modbus encode
The MODBUS Encoder node encodes values appropriately to pass to a fan in node.
This node takes a value or an array of values from a payload and converts them into an array of unsigned 16-bit register values. When passed an array, each element is converted into a sequence of 16-bit registers according to the rules below. Array values are not packed, meaning they never overlap in a single register.
The intended use for this node is to simplify the conversion of values returned in reads into registers when using the MODBUS fan out and fan in nodes. Ensure the datatype size selected matches the number of registers in the fan branch.
String: Pads or limits the string to a fixed length, with a minimum length of 2 bytes. Each register contains two characters. If the payload string is too long, it will be truncated. If too short, it will be padded with nulls (0x00).
64-bit value: Outputs an array of 4 register values.
32-bit value: Outputs an array of 2 register values.
8-bit value: Outputs an array of 1 register value. If "packed" is selected, an array of values will be packed in sequence (big endian) in pairs into 16-bit registers.

---

modbus extract
Extracts values from string buffers (e.g., MODBUS queries).
The node allows you to extract specific bytes for the operation. The normal byte order is represented by letters of the alphabet, for example, an 8-byte buffer has bytes ABCDEFGH.
After selecting the bytes to extract, specify how they should be treated: as unsigned integers, signed integers, or floating-point numbers, along with the appropriate bit length. Alternatively, you can select Bits to extract a single bit from a value in the buffer and express the result as a boolean value by applying a mask.
The operation can be repeated for a series of values by setting the quantity to more than 1. In this case, the payload key will be set to an array of all the values.
If the extracted value is numeric, it can be multiplied by a scale factor and have an offset added to it.
The payload of the emitted message will be set to a table containing all the extracted values, with keys corresponding to the names of the extractions.

---

modbus fanin
This node combines responses from fanned-out MODBUS requests.
All flows from the fan out node should pass back through this node before proceeding to the MODBUS out node.
For detailed information, refer to the MODBUS fan out node documentation.

---

modbus fanout
This node routes MODBUS messages based on query properties.
The fan out and fan in nodes provide the following capabilities:
- Configure MODBUS reads and writes for local data sources (e.g., GPIOs, sensors)
- Route MODBUS reads and writes to other protocols (e.g., BACnet)
- Gateway MODBUS requests to MODBUS RTU/ASCII/TCP devices connected locally
- Firewall MODBUS TCP requests

Usage Guidelines:
The first output from the fan out node must always be directly connected to the fan in node. This connection is used to route errors and signal the fan in node to expect a response, enabling timeout implementation.
Create subsequent outputs by defining rules that match various sets of operations, server IDs, and address ranges.
Set a server ID to route all requests for that specific server to the corresponding fan output. This is useful for gatewaying requests to a local device via the MODBUS request node.
The fan in node will expect a payload matching the request in length (number of registers) or the length of the fan if configured (the number of registers configured). All registers should be returned as an array of numbers (for holding and input) or booleans, "on"/"off" strings, or numbers (0 for false, any other value for true) for discrete input and coil.
If no fan matches the request, a MODBUS error will be returned. If a fan matches but the path does not return a value, the request will timeout.
If "Don't send TCP gateway exceptions" is checked, no exceptions will be sent for request timeouts or unmatched server IDs. This mode is recommended when using the fan with a serial MODBUS responder.

---

modbus in
Receives Modbus requests.
This node handles requests from Modbus clients. If the request is a write operation, the message payload will be populated with the data to be written. Additionally, the message will contain the msg.modbus key, which includes details of the request.
Write data is copied into the payload following these rules:
- If the write operation is for a single value (operation 5 or 6), the payload will contain a single value.
- If the write operation is for multiple values (operation 15 or 16), the payload will contain a table, even if the quantity of values is 1.
- If the write operation is for holding registers, the values will be numbers.
- If the write operation is for coils, the values will be true or false.

---

modbus multipoll
Polls Modbus devices.
The Modbus Multipoll node combines functions from the Modbus Poll and Extract nodes to simplify setting up polls for various register types and remote servers.
Multiple register types and server IDs can be queried simultaneously. If registers of the same type on a single server are contiguous within the configured byte limit, they are polled as a single operation.

The result message contains:
- Interpreted results in the payload.
- Raw, unprocessed register contents (byte strings) of holding or input registers in the key msg.raw.

Holding or input registers can be read in different formats, potentially spanning multiple registers.

Actions triggered by msg.command:
- start - Starts polling. The payload can contain the poll rate in seconds.
- stop - Stops polling.
- queries - Treats the payload as a new list of queries (array of tables with host, unit, start, op, format, swap, offset, multiply, name keys).

Coil/Register Number to Data Address Reference:
  1-9999     -> 0000-270E  Read-Write  Discrete Output Coils
  10001-19999 -> 0000-270E  Read-Only   Discrete Input Contacts
  30001-39999 -> 0000-270E  Read-Only   Analog Input Registers
  40001-49999 -> 0000-270E  Read-Write  Analog Output Holding Registers

Example Input:
msg.command = 'queries'
msg.payload = {
    { name = 'u[1].val1', unit = 1, start = 0, op = 3, format = 'int16le', swap = false, offset = 0, multiply = 1 },
    { name = 'u[1].val2', unit = 1, start = 2, op = 3, format = 'int32le', swap = false, offset = 0, multiply = 1 },
}
msg.command = 'start'
msg.payload = 5  -- Poll rate in seconds

Example Output:
msg.payload = { lat = 0, lon = 0 }
msg.raw = { lat = "\x00\x00\x00\x00", lon = "\x00\x00\x00\x00" }
msg.error = { err1 = "modbus exception: SERVER DEVICE FAILURE" }

---

modbus operation
Creates MODBUS requests.
The MODBUS Operation node generates valid MODBUS requests for both read and write operations.
For read requests, it outputs a valid MODBUS operation attached to each input message.
For write requests, it uses the payload of the incoming message as the value to write.
- Single value write: payload can be a single value or array of one.
- Multiple register write: payload must be an array with entries equal to the number of registers.
- Holding register values must be numbers.
- Coil values: on = true/1/"1"/"on", off = false/0/"0"/"off".

---

modbus out
Sends responses to Modbus requests.
This node sends responses to requests originating from the Modbus in node.
- Write operation: only msg.modbus key is processed.
- Read multiple values: response payload must be a table.
- Read single value: payload can directly contain the value.
- Holding/input registers: values must be numeric.
- Coils/discrete inputs: on = true/1/"1"/"on", off = false/0/"0"/"off".

---

modbus poll
Polls Modbus RTU devices.
The Modbus poll element enables polling of Modbus RTU devices, allowing you to query input or holding registers, coils, or discrete inputs.

Possible outputs:
- Raw string: Contains the data portion of the Modbus response in hexadecimal format.
- Values array: Contains numeric values for registers, 'on' or 'off' for coils and discrete inputs.
- Query, raw string, and values array: Query includes slave ID, operation type, start address (register offset), and count.

---

modbus request
Sends a Modbus request to a Modbus server.
This node sends a raw Modbus request to either a Modbus TCP server or an RTU/ASCII serial server.
The Modbus request is contained in msg.modbus, which includes:
- op - The Modbus operation code.
- start - Starting register offset (for multi-register operations).
- quantity - Number of registers (for multi-register operations).
- address - Register offset (for single-register write operations).
- bytes - Raw bytes for write operations.

If msg.modbus_opts contains host and/or port keys and the requester is in Modbus TCP mode, the requester connects to the specified host and port.

---

modbus requester
Modbus Configuration
Start by selecting the Modbus protocol:
- TCP Mode: Requires specifying the host and port, with optional TLS settings.
- Serial Mode: Requires selecting the serial port and configuring baud rate, parity, etc.

---

modbus responder
Modbus Configuration
Start by selecting the Modbus protocol:
- TCP Mode: Requires specifying the host and port, with optional TLS settings.
- Serial Mode: Requires selecting the serial port and configuring baud rate, parity, etc.
In Serial mode, if the server ID is left empty, the node will process any valid Modbus request it receives.

---

Tutorial PDFs
- MODBUS poll application example: https://cloudgateuniverse.com/system/files/Documentation/modbus_poll_using_the_industrial_serial_card_on_luvitred_v003ext.pdf
- MODBUS Gateway example: https://cloudgateuniverse.com/system/files/Documentation/modbus_gateway_using_the_industrial_serial_card_on_luvitred_v002ext.pdf
