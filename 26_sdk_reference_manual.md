CloudGate SDK Reference Manual
Sources:
  - https://cloudgateuniverse.com/node/4177 (SDK overview)
  - https://cloudgateuniverse.com/node/4179 (Software overview)
  - https://cloudgateuniverse.com/node/4180 (Getting started)
  - https://cloudgateuniverse.com/node/4181 (External packages)
  - https://cloudgateuniverse.com/node/132189 (CloudGate SDK)

The CloudGate SDK includes a full toolchain for building native applications and an extensive API (libcg) that gives access to all device management, provisioning, and networking functions.

SDK Downloads
- Latest SDK: available via CloudGate Universe Library
- Older SDK versions: ftp://ftp.option.com/
- The libcg API reference manual is bundled inside the SDK package (see include/ directory and SDK README).

CloudGate SDK versions:
  SDK version 2.x.x is based on uClibc 0.9.33.2
  SDK version 1.x.x is based on uClibc 0.9.30.3 (no longer supported)
  Note: 32-bit SDK versions are untested and not recommended.

The SDK version used to compile your application determines the minimum firmware version required on the target device.

---

Software Architecture Overview

The CloudGate flash layout consists of:
- Bootloader
- Linux kernel 2.6.35
- Router software: Modified OpenWRT stack
- Firmware customization
- Developer image

Mounted filesystems at runtime:
- /rom           - Root filesystem (read-only). Contains the basic Option firmware (modified OpenWRT).
- /              - Overlay read-write flash filesystem of 20MB. Persists across reboots; wiped by factory reset.
- /tmp           - tmpfs (RAM-backed). Temporary data; lost on power cycle.
- /rom/mnt/cust  - Developer image (read-write, mounted if present and signed).
- /rom/mnt/base_cfg - Firmware customization (mounted if present and signed).

Developer image:
- Maximum size: 30MB.
- Must be signed with the developer key included in the SDK.
- After mounting, the firmware looks for an executable called 'isv' and runs it.

Secure Boot
The iMX.280 processor implements a chain of trust: bootloader -> kernel -> root filesystem -> configuration filesystem -> developer image.
The developer key is distributed with the SDK, allowing anyone to write software for CloudGate.
Custom keys (signed by Option) permanently disable developer mode via a hardware fuse.

---

Build System

The SDK is based on OpenWRT's buildroot system.

Host requirements (Linux, Intel PC, 32-bit or 64-bit):
  make, getopt, fileutils, gcc, ncurses, zlib, awk, flex, unzip, bzip2, xz,
  patch, perl, python (version 2), luajit, wget, tar, find, ccache, cmake, libtool

Officially supported Linux distributions: Ubuntu LTS 18 and Ubuntu LTS 20.
Python version required: 2.7 (use Docker or pyenv if needed).

For cross-compiling luajit from 64-bit host:
  sudo apt-get install libc6-dev-i386

SDK directory structure:
  include/        - Build system scripts (OpenWRT/buildroot)
  keys/           - Developer key for signing images
  config/         - Configuration files bundled with the image
  package/        - Development workspace; create your own applications here
  scripts/        - OpenWRT-related scripts
  target/         - Platform-specific settings
  staging_dir/    - Host tools, compiled packages, header files, assembled filesystem

Build commands:
  make menuconfig    - Configure packages and options
  make               - Build the developer image
  make clean         - Clean the build environment

Output:
  bin/imx28/bundle-sdk-xxx.zip  - Contains compiled code and config files

---

External Packages (OpenWRT Feeds)

Since CloudGate is based on OpenWRT, external feed packages are also available.

Restriction: packages install into the SDK image path (/rom/mnt/cust), not at '/'.
Starting from firmware 1.7.0, the executable search path includes:
  /rom/mnt/cust/bin, /rom/mnt/cust/usr/bin, /rom/mnt/cust/sbin, /rom/mnt/cust/usr/sbin
Libraries are searched in:
  /rom/mnt/cust/lib, /rom/mnt/cust/usr/lib

Working with feeds:
  scripts/feeds update              -- fetch all package definitions
  scripts/feeds search <keyword>    -- search packages (e.g., scripts/feeds search py)
  scripts/feeds install <package>   -- install package and its dependencies

After installing a package, enable it in make menuconfig and run make.

Library path workaround (for packages with hard-coded paths):
  export LD_LIBRARY_PATH=/rom/mnt/cust/lib:/rom/mnt/cust/usr/lib

Reference: http://wiki.openwrt.org/doc/devel/feeds

---

CloudGate Universe Integration

Once an SDK application has been built, debugged, and tested, it can be deployed to a fleet of devices via CloudGate Universe.

Source: https://cloudgateuniverse.com/node/4182

---

Related How-To Guides
- Getting Started: 1_cloudgate_sdk_getting_started.md
- Compiling Your First Image: 2_cloudgate_compiling_first_image.md
- Application Monitoring: 3_cloudgate_application_monitoring.md
- Debugging (gdb): 4_cloudgate_debugging_gdb_support.md
- Configuration Files: 5_cloudgate_conifguration_files.md
- External Packages: 6_cloudgate_external_packages.md
- LuvitRED Binary Package in SDK: 24_how_to_luvitred_binary_package.md
- Building a Kernel Module: 25_how_to_kernel_module.md
- Making Image Persistent: 23_how_to_make_image_persistent.md
