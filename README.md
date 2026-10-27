# Embedded Linux Home Automation Box

### Yocto / Poky • STM32MP135F-DK • Embedded Linux

A custom embedded Linux image developed using the **Yocto Project / Poky (Kirkstone)** for the **STM32MP135F-DK** development board.

The project focuses on building and customizing an embedded Linux system for home automation, including environmental sensor integration, ARM application deployment, automatic startup using systemd, and package management.

## Table of Contents

- [Project Overview](#project-overview)
- [Hardware and Technologies](#hardware-and-technologies)
- [Yocto Build Architecture](#yocto-build-architecture)
- [Application Integration and systemd Service](#application-integration-and-systemd-service)
- [Repository Contents](#repository-contents)
- [Deployment Artifacts](#deployment-artifacts)
- [Security Considerations](#security-considerations)
- [Project Limitations](#project-limitations)
- [Author](#author)

## Project Overview

The objective of this project is to develop and customize an embedded Linux image using the Yocto Project for a home automation application.

The system targets the STM32MP135F-DK development board and integrates an application designed to communicate with a Si7021 temperature and humidity sensor.

The main technical objectives are:

- Build a custom embedded Linux image using Yocto and BitBake.
- Integrate a temperature and humidity sensor through the I²C interface.
- Deploy a cross-compiled ARM application into the Linux root filesystem.
- Configure automatic application startup using systemd.
- Include OPKG for software package management.
- Explore the configuration and deployment of Linux systems on resource-constrained embedded platforms.

## Hardware and Technologies

### Hardware

- **Development board:** STM32MP135F-DK
- **Environmental sensor:** Si7021 temperature and humidity sensor
- **Storage:** microSD card
- **Communication interfaces:** I²C and UART

### Software and Tools

- Yocto Project / Poky (Kirkstone)
- BitBake
- Embedded Linux
- ARM cross-compilation
- I²C and UART interfaces
- systemd
- OPKG
- Linux kernel

## Yocto Build Architecture

The Yocto Project provides a framework for building customized Linux distributions for embedded systems.

This project targets the STM32MP13 platform and uses a custom image named `core-image-ubs-homeautomation`.

The main architectural components include:

| Component | Role |
|---|---|
| Poky | Yocto reference distribution and build environment |
| BitBake | Build engine for processing recipes and dependencies |
| STM32MP13 board support | Hardware-specific configuration and integration |
| Linux kernel | Operating system kernel for the target platform |
| `homeauto-app` | ARM application for environmental sensor communication |
| systemd | Service management and application startup |
| OPKG | Software package management |

The Yocto build system uses layers, recipes, and configuration files to define the software packages included in the generated image.

The archived package manifest confirms the inclusion of the custom `homeauto-app` package, `i2c-tools`, and Linux kernel packages.

## Application Integration and systemd Service

The custom Yocto image includes `homeauto-app`, a compiled 32-bit ARM application designed to acquire temperature and humidity measurements from a Si7021 sensor through I²C and transmit measurements through UART.

Static analysis of the executable identified the following interfaces and functions:

- **I²C:** `/dev/i2c-1`, used for sensor communication.
- **UART:** `/dev/ttySTM0`, used for serial communication.
- **Linux interfaces:** `open()`, `read()`, `write()`, and `ioctl()`.

The generated Linux root filesystem contains the executable at:

`/usr/bin/homeauto-app`

The presence of this file was verified by inspecting the archived `.ext4` root filesystem.

### Automatic Startup with systemd

A custom systemd service, [`homeauto.service`](homeauto.service), is included in the image to manage application startup.

The service configuration is:

```ini
[Unit]
Description=Service Domotique UBS Si7021
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/homeauto-app
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

This configuration provides:

- Automatic application startup when the enabled service is activated during boot.
- Automatic restart if the application process terminates.
- A five-second delay between restart attempts.

Inspection of the generated root filesystem confirmed the presence of:

- `/lib/systemd/system/homeauto.service`
- `/etc/systemd/system/multi-user.target.wants/homeauto.service`

The second path is a symbolic link that confirms the service is enabled for `multi-user.target`.

### Build Evidence

The repository includes the following technical artifacts:

- [`homeauto-app`](homeauto-app): compiled ARM executable recovered from the build outputs.
- [`homeauto.service`](homeauto.service): systemd service configuration.
- [`image.manifest`](image.manifest): package manifest of the generated Yocto image.
- [`FlashLayout_sdcard_stm32mp135f-dk-optee.tsv`](FlashLayout_sdcard_stm32mp135f-dk-optee.tsv): STM32 flash partition layout.

These artifacts demonstrate application packaging, integration into the Linux root filesystem, and systemd service configuration.

**Verification scope:** The archived artifacts confirm build-time integration and service enablement. Successful execution on the physical board has not been independently revalidated from these artifacts.

## Repository Contents

This repository provides technical documentation and selected artifacts from the embedded Linux home automation project.

The original development environment was hosted on university infrastructure because Yocto builds require substantial storage and computational resources.

A separate project archive is available here:

**[Yocto Project Archive (Google Drive)](https://drive.google.com/file/d/1K6YCgpWTaJXH-AVsfeb3520LOSip9Ydv/view?usp=drive_link)**

The contents and completeness of this archive should be verified before using it to reproduce the build.

The complete Yocto development environment is not tracked directly in this repository because of its size.

The repository currently provides compiled application and deployment artifacts rather than a complete, independently verified source-based build environment.

## Deployment Artifacts

The archived Yocto build outputs include separate filesystem images for the STM32MP135F-DK platform:

- `st-image-bootfs-poky-stm32mp13-disco.ext4`
- `st-image-vendorfs-poky-stm32mp13-disco.ext4`
- `core-image-ubs-homeautomation-stm32mp13-disco.ext4`
- `st-image-userfs-poky-stm32mp13-disco.ext4`

The `FlashLayout_sdcard_stm32mp135f-dk-optee.tsv` file describes the target partition layout, including boot firmware components and filesystem partitions.

The layout references boot components associated with the STM32 boot chain, including TF-A, FIP, and an OP-TEE-related configuration. Their presence in the layout does not by itself demonstrate that authenticated Secure Boot was enabled.

The complete flashing procedure has not yet been revalidated.

**Important:** Individual `.ext4` filesystem images must not be treated as complete SD-card disk images. Deployment requires the appropriate STM32 flashing procedure and compatible boot components.

## Security Considerations

Security is an important aspect of embedded Linux development, particularly for connected devices such as home automation systems.

This project provides a basis for studying several embedded Linux security mechanisms:

- **System minimization:** Reducing the attack surface by limiting unnecessary packages and services.
- **Privilege management:** Applying the principle of least privilege to applications and system services.
- **Secure Boot:** Studying hardware-supported chains of trust and firmware integrity verification.
- **Firmware protection:** Understanding mechanisms used to protect boot components and software integrity.
- **Software updates:** Considering package authenticity, integrity, and secure update mechanisms.
- **Service reliability:** Using systemd to manage application lifecycle and automatic restart.

These topics represent security considerations and possible extensions. They should not be interpreted as fully implemented or validated security features of the generated STM32 image.

## Author

**Meysoun Gharbi**

Master's Student in Cybersecurity of Embedded Systems  
Université Bretagne Sud, France
