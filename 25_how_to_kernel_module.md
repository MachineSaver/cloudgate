Build your own Kernel Module
Source: https://cloudgateuniverse.com/node/132196

This document explains the steps to build your own kernel module for CloudGate.

Requirements
- Linux kernel sources: linux-imx28_customer.tar.bz2 (delivered together with firmware releases)
- CloudGate SDK: CloudGate-SDK....tar.bz2

Preparation

Unpack both tarballs:
  mkdir linux_kernel
  tar -C linux_kernel -jxvf linux-imx28_customer.tar.bz2
  tar -jxvf CloudGate-SDK....tar.bz2

Creating the Kernel Module

Example: Hello World
(A dummy kernel module which prints a log line to the kernel log.)

Files needed: hello.c and Makefile
(Download from the CloudGate developer docs section.)

Makefile configuration:
- Change KDIR to the absolute path of the linux_kernel directory.
- Change CROSS_COMPILE to the absolute path of the CloudGate SDK directory.
- Change CCDIR and LD to use the correct uClibc version:
    SDK v1 -> toolchain-arm_v5te_gcc-4.4.3_uClibc-0.9.30.3_eabi
    SDK v2 -> toolchain-arm_v5te_gcc-4.4.3_uClibc-0.9.33.2_eabi

Build:
  make           -- builds the kernel module
  make clean     -- cleans the directory

Deployment:
Copy the resulting hello.ko file to the device and run:
  insmod hello.ko
