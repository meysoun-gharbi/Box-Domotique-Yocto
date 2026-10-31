# Embedded Linux Home Automation Box

**Yocto Project | STM32MP135F-DK | Embedded Linux | I²C | systemd | ARM**

## Overview

This project focuses on developing and customizing an embedded Linux system for a home automation application using the **Yocto Project / Poky (Kirkstone)**.

The target platform is the **STM32MP135F-DK** development board, integrating a Si7021 temperature and humidity sensor, a custom ARM application, and automatic application startup through systemd.

The project explores embedded Linux image customization, hardware communication, application integration, service management, and deployment on an ARM-based platform.

### Main Objectives

- Build a customized embedded Linux image using Yocto and BitBake.
- Integrate a Si7021 temperature and humidity sensor through I²C.
- Deploy an ARM application into the Linux root filesystem.
- Configure automatic application startup using systemd.
- Include OPKG for package management.
- Study the architecture and deployment of embedded Linux systems.

## Technical Environment

| Component | Technology |
|---|---|
| Development board | STM32MP135F-DK |
| Processor architecture | ARM |
| Embedded Linux build system | Yocto Project / Poky (Kirkstone) |
| Build automation | BitBake |
| Custom image | `core-image-ubs-homeautomation` |
| Environmental sensor | Si7021 |
| Sensor interface | I²C |
| Serial communication | UART |
| Service management | systemd |
| Package management | OPKG |
| Storage | microSD card |
| Development environment | Remote university build server |

## System Architecture

The embedded system is organized around the STM32MP135F-DK board, which runs a customized Linux image containing the home automation application.

```text
             +-------------------------+
             |    Si7021 Sensor        |
             | Temperature / Humidity  |
             +------------+------------+
                          |
                          | I2C
                          v
             +-------------------------+
             |      STM32MP135F-DK     |
             |                         |
             |    Embedded Linux       |
             |    Yocto / Poky         |
             |                         |
             |  +-------------------+  |
             |  |   homeauto-app    |  |
             |  |   ARM Executable  |  |
             |  +---------+---------+  |
             |            |            |
             |     systemd Service     |
             |     homeauto.service    |
             +------------+------------+
                          |
                          | UART
                          v
             +-------------------------+
             |   Serial Communication  |
             +-------------------------+
```

The application communicates with the Si7021 sensor through I²C and uses UART for serial communication.

## Yocto Build Architecture

The Yocto Project provides a framework for building customized Linux distributions for embedded systems.

The project uses Poky (Kirkstone), BitBake, and STM32MP13 platform support to generate a Linux image targeting the STM32MP135F-DK.

| Component | Role |
|---|---|
| Poky | Yocto reference distribution and build environment |
| BitBake | Processes recipes and dependencies |
| STM32MP13 board support | Hardware-specific configuration |
| Linux kernel | Core operating system functionality |
| `homeauto-app` | Custom ARM application |
| systemd | Service management and automatic startup |
| OPKG | Package management |

The generated image is named:

`core-image-ubs-homeautomation`

The archived package manifest provides evidence of the inclusion of the custom `homeauto-app` package, `i2c-tools`, and Linux kernel packages.

## Application Integration

### Home Automation Application

The project includes a compiled 32-bit ARM application:

[`app/homeauto-app`](app/homeauto-app)

The application is designed to acquire environmental measurements from the Si7021 sensor through I²C and transmit data through UART.

Static analysis of the executable identified the following interfaces:

| Interface | Purpose |
|---|---|
| `/dev/i2c-1` | I²C communication with the sensor |
| `/dev/ttySTM0` | UART communication |
| `open()` | Access Linux device files |
| `read()` | Read data |
| `write()` | Write data |
| `ioctl()` | Perform device-specific operations |

The application was integrated into the generated root filesystem at:

`/usr/bin/homeauto-app`

Its presence was verified through inspection of the archived `.ext4` root filesystem.

### Automatic Startup Using systemd

The project includes a custom systemd service:

[`systemd/homeauto.service`](systemd/homeauto.service)

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

This configuration provides automatic application startup when the enabled service is activated, automatic restart after process termination, and a five-second delay between restart attempts.

Inspection of the generated root filesystem confirmed the presence of:

- `/lib/systemd/system/homeauto.service`
- `/etc/systemd/system/multi-user.target.wants/homeauto.service`

The second path is a symbolic link indicating that the service is enabled for `multi-user.target`.

**Validation scope:** These artifacts confirm application integration and service enablement. Successful execution on the physical board has not been independently revalidated from the archived files.

## Repository Structure

```text
Box-Domotique-Yocto/
├── README.md
├── .gitignore
│
├── app/
│   └── homeauto-app
│
├── systemd/
│   └── homeauto.service
│
├── yocto/
│   └── image.manifest
│
└── flashing/
    └── FlashLayout_sdcard_stm32mp135f-dk-optee.tsv
```

### Repository Contents

| File | Description |
|---|---|
| [`app/homeauto-app`](app/homeauto-app) | Compiled ARM home automation application |
| [`systemd/homeauto.service`](systemd/homeauto.service) | systemd service configuration |
| [`yocto/image.manifest`](yocto/image.manifest) | Package manifest of the generated Yocto image |
| [`flashing/FlashLayout_sdcard_stm32mp135f-dk-optee.tsv`](flashing/FlashLayout_sdcard_stm32mp135f-dk-optee.tsv) | STM32MP135F-DK flash partition layout |

These files document the available application, image composition, and deployment artifacts.

## Deployment Artifacts

The archived Yocto build outputs include the following filesystem images:

```text
st-image-bootfs-poky-stm32mp13-disco.ext4
st-image-vendorfs-poky-stm32mp13-disco.ext4
core-image-ubs-homeautomation-stm32mp13-disco.ext4
st-image-userfs-poky-stm32mp13-disco.ext4
```

The flashing layout file describes the target partition structure and references components associated with the STM32 boot chain, including TF-A, FIP, and an OP-TEE-related configuration.

**Important:** The presence of these boot components in the flashing layout does not demonstrate that authenticated Secure Boot was enabled.

Individual `.ext4` filesystem images are not complete SD-card disk images. Deployment requires the appropriate STM32 flashing procedure and compatible boot components.

The complete flashing procedure has not been independently revalidated.

## Build Environment and Reproducibility

The Yocto build environment was hosted on a remote university server due to the significant computational and storage resources required for embedded Linux image generation.

The original BitBake recipes, custom layers, and build configurations are no longer accessible through that server.

Consequently, this repository contains the available compiled application, systemd service configuration, package manifest, and flashing layout, but **does not contain a complete reproducible Yocto build environment**.

A separate project archive is available:

[Yocto Project Archive — Google Drive](https://drive.google.com/file/d/1K6YCgpWTaJXH-AVsfeb3520LOSip9Ydv/view?usp=drive_link)

The contents and completeness of this archive have not been independently verified for full build reproduction.

Rebuilding the original image from this GitHub repository alone is therefore not currently possible.

## Security Considerations

Embedded Linux security is particularly important for connected devices such as home automation systems.

This project provides a basis for studying:

- **System minimization:** Reducing unnecessary packages and services.
- **Privilege management:** Applying least-privilege principles.
- **Secure Boot:** Understanding hardware-supported chains of trust.
- **Firmware integrity:** Protecting boot components against unauthorized modification.
- **Secure software updates:** Verifying the authenticity and integrity of updates.
- **Service reliability:** Managing application lifecycle using systemd.

These topics represent security considerations and potential future improvements. They should not be interpreted as fully implemented or experimentally validated security protections in the generated image.

## Project Limitations

The main limitations are:

-
