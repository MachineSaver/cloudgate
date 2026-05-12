LuvitRED MQTT Nodes
Source: https://cloudgateuniverse.com/docs/nodes

This document covers all MQTT-related nodes available in LuvitRED for CloudGate.

---

mqtt control
MQTT control node. Allows broker parameters to be dynamically set and the broker controlled from a flow.
Expects a payload containing a table with a key command set to the string:
- "update" - updates the broker configuration. Keys: host, port, use_tls, reject_unauthorized, username, password, client_id.
- "config" - retrieves the broker configuration.
- "enable" - enables the broker connection.
- "disable" - disables the broker connection.

Start disabled can be checked in the MQTT broker config node to allow the broker config to be set before a connection attempt. In this case an "enable" command must be issued after the "update" command.

Example:
msg.payload = { command = 'update', username = 'username', password = 'password' }
msg.payload = { command = 'config' }
msg.payload = { command = 'enable' }
msg.payload = { command = 'disable' }

---

mqtt in
The MQTT Input node connects to an MQTT broker and subscribes to the specified topic. The topic may include MQTT wildcards for flexible subscription.

Output message properties:
- msg.topic: The topic of the received MQTT message.
- msg.payload: The payload of the MQTT message, as a string.
- msg.qos: The Quality of Service level.
- msg.retain: Whether the message was sent as a retained message.
- msg.duplicate: Whether the message is a duplicate.

Topic wildcards:
- #: Matches any level of the hierarchy (must be last character). Example: SENSOR/# matches SENSOR/1/TEMP.
- +: Matches a single level between delimiters. Example: SENSOR/+/TEMP matches SENSOR/1/TEMP.

Dynamic topic variables (Mustache syntax):
- {{serial}}: Replaced with the CloudGate serial number.
- {{global.variable}}: Selects a variable from the global function context.

MQTT v5 additional properties on output:
msg.expiry = 20
msg.response_topic = 'topic/resp'
msg.correlation_data = '1234'
msg.content_type = 'application/json'
msg.props = { {'prop1', 'val1'}, {'prop2', 'val2'} }

---

mqtt out
Connects to an MQTT broker and publishes msg.payload to the topic specified in msg.topic or the one set in the node's edit window (message topic takes priority).

msg.qos and msg.retain override node edit panel settings.
If msg.payload is a table, it will be converted to a JSON string before being sent.

Connection behavior:
- Continuous: The node attempts to maintain a continuous connection to the broker.
- Polled: Connects periodically based on the configured polling interval.
- Immediate (if enabled): Attempts immediate connection upon receiving a message while disconnected.

If queuing is disabled and the node is disconnected, messages will be discarded.

Dynamic topic variables (Mustache syntax):
- {{serial}}: Replaced with the CloudGate serial number.
- {{global.variable}}: Selects a variable from the global function context.

Example input:
msg.payload = 'payload'
msg.topic = 'topic/req'
msg.qos = 2
msg.retain = false
-- MQTT v5 additional properties:
msg.expiry = 20
msg.response_topic = 'topic/resp'
msg.correlation_data = '1234'
msg.content_type = 'application/json'
msg.props = { {'prop1', 'val1'}, {'prop2', 'val2'} }

---

mqtt-broker
MQTT Broker Configuration
The MQTT Broker Configuration allows you to set parameters for connecting to the MQTT broker.
Both TCP and TLS connections are supported.
In TLS mode, selecting 'validate server' verifies the server certificate against root CAs. If not checked, the connection could be vulnerable to man-in-the-middle attacks.

Connection strategies:
- Continuous: LuvitRED attempts to maintain a continuous connection. If the connection is lost, it retries after a configurable time.
- Polled: LuvitRED connects on a polled basis and remains connected until a configurable period of inactivity.

Standard MQTT parameters:
- Client ID: MQTT client identifier. Defaults to the gateway Serial Number if left blank.
- Username: MQTT username for authentication.
- Password: MQTT password for authentication.
- Keep Alive: Enables MQTT PING and PONG messages. Default is every 60 seconds. Set to 0 to disable.
- ACK Timeout: Timeout for message acknowledgment by the broker.
- Clean Session: Determines the session persistence behavior.
- MQTT Protocol Version: Allows compatibility with older servers. Version 3.1 can be selected if needed.
- Start Disabled: If using the MQTT Control node to set server parameters, the broker can be initially disabled.

Use Case Reference: https://cloudgateuniverse.com/node/100053 (Connecting to an MQTT Broker example)
