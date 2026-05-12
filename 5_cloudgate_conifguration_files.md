Configuration files
CloudGate uses the UCI configuration system from OpenWRT. See http://wiki.openwrt.org/doc/uci for more information. However, the UCI system is extended to provide multi-layer configuration capabilities.

 

Configurations can be defined in different layers and layers can override settings in lower layers, but lower layers can also set sections to read-only, which will lock that section and prevent changes by higher layers.

 

To set a section to read-only, the following option needs to be added to the section: “option read-only 1”.

 

Note:
The original UCI implementation supports unnamed or anonymous sections. Because of the layered setup, all sections need to be named in order to merge them properly.

 

The following layers are defined (top to bottom):

 

User Settings: Stored in /etc/config
Developer Settings: Stored in /rom/mnt/cust_cfg
Base Settings: Stored in /rom/mnt/base_cfg
Firmware Settings: Stored in /rom/etc/config
 

The Firmware Settings layer is the lowest layer. This is the configuration present in the firmware itself.  This config layer can only be changed by a firmware update.

 

Base Settings can be modified by flashing a configuration image on the CloudGate. This can also be deployed from the provisioning server.

 

The Developer Settings layer is a configuration layer that can be bundled together with an SDK image.
The generated bundle-sdk-xxx.zip is an image which contains BOTH the SDK image and the config image. When upgraded, the config image will be mounted under /rom/mnt/cust_cfg.

 

User Settings is the top-level configuration layer and the only one with read&write permissions. Settings that are changed on the web UI level or through the console/shell are stored in this layer.

 

Each layer can override settings in the layer below it. User Settings can override Developer Settings, which in turn can overrride Base Settings, etc...
Unless a section is specified as read-only: in that case layers above cannot override that section.
