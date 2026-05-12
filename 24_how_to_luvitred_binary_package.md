Implementing a LuvitRED Binary Package in your CloudGate SDK
Source: https://cloudgateuniverse.com/node/85145

This document explains how to implement a LuvitRED binary package in your CloudGate SDK.

Step 1 — Download the LuvitRED binary package via FTP

Using a FTP client (e.g., FileZilla):
1. Install FileZilla.
2. Enter:
   - Host: ftp://ftp.option.com/
   - Username: OptionLuvitred
   - Password: <REDACTED>
3. Click Quick Connect and then OK.
4. Select the folder 'luvitred' from the Remote site panel.
5. Drag and drop the latest luvitred binary file to the desired local location.

Note: Use at least the SDK version matching the LuvitRED version.
See release notes here: https://cloudgateuniverse.com/node/85145

Step 2 — Unpack the binary package

Save and untar the LuvitRED binary package in your CloudGate SDK directory.

In sdk/package several packages are copied to be able to run LuvitRED.
LuvitRED itself is installed in the package/luvit-bin directory.

Note: Read the README file in the SDK root directory and follow the instructions.

Step 3 — Include LuvitRED in the next build

1. Select "luvit-bin" in make menuconfig > base system menu.
2. Save changes and exit menuconfig.
3. Clean the compiling environment: make clean
4. Build the customer image: make
5. Find the bundle-sdk-xxx.zip file in bin/imx28. It contains both the compiled code and the config files.
