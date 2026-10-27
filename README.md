# Embedded Linux Home Automation Box

### Yocto / Poky • STM32MP135F-DK • Embedded Linux

A custom embedded Linux distribution developed using **Yocto Project / Poky (Kirkstone)** for the **STM32MP135F-DK** development board.

The project focuses on building and configuring an embedded Linux system for home automation, including environmental sensor integration, automatic application startup, and package management.

## Table of Contents

- [Project Overview](#project-overview)
- [Hardware and Technologies](#hardware-and-technologies)
- [Yocto Build Architecture](#yocto-build-architecture)
- [Repository Contents](#repository-contents)
- [Quick Start: Flashing the Image](#quick-start-flashing-the-image)
- [Building from Source](#building-from-source)
- [Security Considerations](#security-considerations)
- [Project Limitations](#project-limitations)
- [Author](#author)

## Project Overview

The objective of this project is to explore the development and customization of an embedded Linux distribution using the Yocto Project.

The system is designed around the STM32MP135F-DK development board and a Si7021 environmental sensor.

The main technical objectives include:

- Building a custom Linux image using Yocto and BitBake.
- Integrating a temperature and humidity sensor through the I²C interface.
- Configuring automatic application startup using systemd.
- Integrating OPKG for software package management.
- Understanding the configuration and deployment of embedded Linux systems on hardware-constrained platforms.

## Hardware and Technologies

### Hardware

- **Development board:** STM32MP135F-DK
- **Environmental sensor:** Si7021 temperature and humidity sensor
- **Storage:** microSD card

### Software and Tools

- Yocto Project / Poky (Kirkstone)
- BitBake
- Embedded Linux
- I²C
- systemd
- OPKG
- Yocto SDK and cross-compilation

## Yocto Build Architecture

The distribution is built using the Yocto Project, which provides a framework for generating customized Linux systems for embedded devices.

The project targets the STM32MP13 platform and focuses on integrating the software components required for the home automation application.

The principal components are:

- **Poky:** Reference distribution and build environment.
- **BitBake:** Build engine responsible for processing recipes and their dependencies.
- **Board-specific configuration:** Hardware support for the STM32MP135F-DK.
- **systemd:** Service management and automatic application startup.
- **I²C interface:** Communication with the environmental sensor.
- **OPKG:** Package management for the embedded Linux system.

The Yocto build system uses layers, recipes and configuration files to define the software included in the generated image.

## Repository Contents

This repository documents the embedded Linux distribution project and provides instructions for inspecting, building and deploying the system.

The original development environment was hosted on university infrastructure because Yocto builds require significant storage and computational resources.

A backup of the project environment is available separately:

**[Yocto Project Archive (.tar.gz)](https://drive.google.com/file/d/1K6YCgpWTaJXH-AVsfeb3520LOSip9Ydv/view?usp=drive_link)**

The archive contains the saved project environment. Its contents and directory structure should be inspected before attempting to reproduce the build.

The complete build environment is not tracked directly in this Git repository due to its size.

## Quick Start: Flashing the Image

The following procedure describes how to flash a generated Yocto disk image onto a microSD card.

### Requirements

- STM32MP135F-DK development board
- Compatible microSD card
- Linux computer
- Generated image: `core-image-ubs-homeautomation-stm32mp13-disco.wic.gz`

### 1. Obtain the image

Locate the generated `.wic.gz` image in the Yocto build output or project archive, if available.

### 2. Decompress the image

```bash
gunzip core-image-ubs-homeautomation-stm32mp13-disco.wic.gz
```

This produces the uncompressed `.wic` disk image.

### 3. Identify the target device

```bash
lsblk
```

Identify the device corresponding to your microSD card.

**Warning:** Selecting the wrong device can permanently destroy existing data. Verify the device path before proceeding.

### 4. Flash the image

Replace `/dev/sdX` with the actual device representing the entire microSD card.

```bash
sudo dd \
  if=core-image-ubs-homeautomation-stm32mp13-disco.wic \
  of=/dev/sdX \
  bs=8M \
  status=progress \
  conv=fdatasync
```

Wait for the operation to finish before removing the card.

### 5. Boot the board

Insert the microSD card into the STM32MP135F-DK development board and power on the system.

The boot procedure depends on the board configuration and the generated image.

## Building from Source

The original Yocto environment is stored in the project archive.

### 1. Extract the archive

```bash
tar -xzvf sauvegarde_yocto_complet.tar.gz
```

Navigate to the extracted project directory.

### 2. Initialize the Yocto environment

If the extracted environment contains the Poky directory, navigate to it:

```bash
cd poky
source oe-init-build-env
```

The initialization script sets up the build environment.

### 3. Verify the build configuration

Before compilation, verify:

- The machine configuration targets the appropriate STM32MP13 development board.
- All required Yocto layers are available.
- `bblayers.conf` contains the correct layer paths.
- `local.conf` contains the required build settings.
- Any project-specific recipes and dependencies are available.

### 4. Build the image

```bash
bitbake core-image-ubs-homeautomation
```

This command assumes the custom image recipe is present in the restored build environment.

**Note:** Rebuilding the project requires sufficient disk space, memory and compilation time. The original build environment was hosted on university infrastructure, and the restored archive has not yet been independently revalidated.

## Security Considerations

Security is an important aspect of embedded Linux development, particularly for connected devices such as home automation systems.

The project also provides a basis for studying several embedded Linux security mechanisms:

- **System minimization:** Reducing the attack surface by limiting unnecessary packages and services.
- **Privilege management:** Applying the principle of least privilege to applications and system services.
- **Secure Boot:** Studying hardware-supported chains of trust and firmware integrity verification.
- **Firmware protection:** Understanding mechanisms used to protect boot components and software integrity.
- **Software updates:** Considering package authenticity, integrity and secure update mechanisms.

These topics represent security considerations and potential extensions. They should not be interpreted as fully implemented or validated security features of the STM32 image.

## Project Limitations

The original development and build process was performed on university infrastructure.

The available archive preserves the project environment, but its exact contents and build reproducibility still require verification.

The repository therefore distinguishes between the documented project configuration, the original development objectives and security mechanisms considered for further study.

## Author

**Meysoun Gharbi**

Master's Student in Cybersecurity of Embedded Systems  
Université Bretagne Sud, France
