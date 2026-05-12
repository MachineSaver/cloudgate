LoRa Basics Station Configuration
Sources:
  - https://cloudgateuniverse.com/node/133357 (LoRa Basics Station)
  - https://cloudgateuniverse.com/docs/lora-gateway (LoRa Gateway overview)

The LoRa Basics Station protocol simplifies management of large scale LoRaWAN networks. LoRa Basics Station is the preferred way of connecting Gateways to most LoRaWAN Network Servers.

LoRa Basics Station website: https://doc.sm.tc/station/

---

Installing Basics Station on CloudGate

BasicStation binaries are available on the FTP server:
  ftp://ftp.option.com/basicstation

Via local User Interface
1. Login to CloudGate Universe: https://cloudgateuniverse.com/
2. Go to Library.
3. In the Application section, click on "View application".
4. Locate the Basics Station application.
5. Click "View details" and use the download icon to download the application.

Via CloudGate Universe Provisioning
Follow the instructions in the provisioning tutorial (available in CloudGate Universe Library).

---

Connecting CloudGate to LoRaWAN Network Servers

The Things Stack (TTI)
For connecting CloudGate to TTI using LoRa Basics Station:
- CloudGate documentation: https://cloudgateuniverse.com/system/files/Documentation/connect_cloudgate_to_tti_v1.2.pdf
- TheThingsStack documentation: https://www.thethingsindustries.com/docs/

AWS IoT Core for LoRaWAN
https://docs.aws.amazon.com/iot-wireless/latest/developerguide/lorawan-manage-gateways.html

Senet
https://docs.senetco.io/dev/gw/BasicStation/

---

LoRa Gateway Configuration (LuvitRED)
Source: https://cloudgateuniverse.com/docs/lora-gateway

CloudGate can function as a complete LoRaWAN solution in three primary network configurations:
1. Private Single-Site Network: CloudGate serves as both LoRaWAN server and gateway.
2. Private Multi-Site Network: Multiple CloudGates with one as server, others as gateways.
3. Public Multi-Site Network: CloudGate as a gateway connecting to third-party LoRaWAN servers.

Requirements
- A CloudGate device with a compatible LoRa card (see 28_lora_card_family.md)
- Latest firmware and LuvitRED version installed
- Appropriate LoRa card for your region and sensor frequency range

Supported LoRa Cards:
  CG2132: EU868, RU864, IN865, US915, AU915, AS923
  CG8102: EU868, RU864, IN865, US915, AU915, AS923
  (See 28_lora_card_family.md for full list including EOL models)

LoRaWAN Device Activation Methods

OTAA (Over-The-Air Activation) requires:
- Device EUI (e.g., AF5005681BFB7CE0)
- Application Key (e.g., 2B7E151628AED2A6ABF7158809CF4F3C)

ABP (Activation By Personalization) requires:
- Device Address (e.g., 391B83AB)
- Application Session Key (e.g., E4FAE60E9E368749AEE29BD7832BD866)
- Network Session Key (e.g., 7D19813D5E25991F192AB78D890E31BF)

Basic LuvitRED Configuration Steps
1. In the LuvitRED editor, add a lora device node to your workspace.
2. Open the node and select the appropriate activation method (OTAA or ABP).
3. Enter the required address and keys for your sensor.
4. Create a new application configuration (pencil icon).
5. Within the application configuration, create the server configuration.
6. In the server configuration, create the forwarder configuration.
7. Click through all the "add/ok" buttons to complete setup.
8. Attach a debug node to the output for monitoring.
9. Deploy the flow.

LoRa Node Message Properties
  msg.payload    - The payload data
  msg.port       - The port used for communication
  msg.snr        - Signal-to-noise ratio
  msg.rssi       - Received signal strength indicator
  msg.DevAddr    - Device address

Decoding Sensor Data
- Connect a custom decoder function to the LoRa device output node to interpret uplink data.
- For downstream communication, develop an encoder and connect it to the LoRa device input.
- Some manufacturers (nke, elvaco, elsys) are supported by the lora-codec node.

LORIOT Integration
Source: https://cloudgateuniverse.com/node/149943

LORIOT provides both public and private LoRaWAN network servers.
Website: https://www.loriot.io/

Two connection methods:
1. Loriot Agent (available in the CloudGate applications library). Select the correct version for your expansion card.
2. Via LuvitRED as a "Semtech Packet Forwarder":
   - In LORIOT, configure a Semtech gateway with a MAC address (e.g., 11:22:33:44:55:66).
   - In LuvitRED, set the forwarder ID to 112233FFFF445566.
   - Frequency plans: EU868_Semtech (default), US915_CH0_15, EU433_Min.
   - Add a UDP request and lora forwarder node to the LuvitRED Advanced Editor workspace.
   - Set the UDP request node to connect to eu1.loriot.io port 1780 and bind to local port 1780.
   - Select "automatically open a hole in the firewall".
   - Note: The gateway status in LORIOT will always show as disconnected but it functions correctly.
