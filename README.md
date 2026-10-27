
# Embedded Linux Home Automation Box
### Yocto / Poky • STM32MP135F-DK • Embedded Systems

A customized embedded Linux distribution developed with **Yocto Project / Poky (Kirkstone)** for the STM32MP135F-DK development board.

The project transforms the board into a home automation platform capable of collecting environmental sensor data, automatically running applications at startup, and supporting remote software package updates.

## Table of Contents

- [Project Overview](#project-overview)
- [Hardware and Technologies](#hardware-and-technologies)
- [Validated Features](#validated-features)
- [System Architecture](#system-architecture)
- [Repository Contents](#repository-contents)
- [Quick Start: Flashing the Image](#quick-start-flashing-the-image)
- [Building from Source](#building-from-source)
- [Security Considerations](#security-considerations)
- [Possible Improvements](#possible-improvements)
- [Author](#author)

## Project Overview

The objective of this project is to build a custom Linux distribution for an embedded system using Yocto.

The system integrates an I²C environmental sensor, manages application startup through systemd, and provides package management capabilities for software updates.

## Hardware and Technologies

### Hardware

- **Development board:** STM32MP135F-DK
- **Environmental sensor:** Si7021 temperature and humidity sensor
- **Storage:** microSD card

### Software and Technologies

- Yocto Project / Poky — Kirkstone
- BitBake
- Embedded Linux
- I²C
- systemd
- OPKG package manager
- Yocto SDK and cross-compilation

## Validated Features

| Feature | Technology | Status |
|---|---|---|
| Custom Linux image | Yocto / Poky (Kirkstone) | Validated |
| Custom Yocto layer | `meta-ubs-homeautomation` | Validated |
| Environmental data acquisition | Si7021 over I²C | Validated |
| Automatic application startup | systemd / `sensor-app` | Validated |
| Remote package updates | OPKG over HTTP | Validated |
| Cross-development environment | Yocto SDK | Generated |

##  Architecture Yocto & Layers
- **Distribution de base** : Yocto Project / Poky
- **Target Hardware** : Raspberry Pi (`meta-raspberrypi`)
- **Layers de sécurité & virtualisation** : `meta-security`, `meta-virtualization`
- **Layer personnalisée** : Création de recettes `.bb` et `.bbappend` pour l'intégration des règles AppArmor et la configuration de l'image minimale durcie

## Repository Contents

The repository provides project documentation and instructions for using the prebuilt image or rebuilding the distribution.

The complete Yocto build environment is distributed separately because it requires several gigabytes of storage.

**Complete source archive:**

[Download the complete Yocto project (.tar.gz)](https://drive.google.com/file/d/1K6YCgpWTaJXH-AVsfeb3520LOSip9Ydv/view?usp=drive_link)

The archive contains the project sources and build configuration. Its directory structure should be checked before following the build instructions.

## Quick Start: Flashing the Image

This procedure allows you to test the prebuilt image without rebuilding the entire Yocto environment.

### Requirements

- STM32MP135F-DK development board
- Compatible microSD card
- Linux computer
- Prebuilt image: `core-image-ubs-homeautomation-stm32mp13-disco.wic.gz`

### 1. Obtain the image

Download the prebuilt image from the project's release or artifact storage.

### 2. Decompress the image

```bash
gunzip core-image-ubs-homeautomation-stm32mp13-disco.wic.gz
```

This produces the uncompressed `.wic` disk image.

### 3. Identify the target device

```bash
lsblk
```

Identify the correct device corresponding to your microSD card.

**Warning:** Selecting the wrong device can permanently destroy existing data. Verify the device path before proceeding.

### 4. Flash the image

Replace `/dev/sdX` with the actual device representing the entire microSD card, not an individual partition.

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

Insert the microSD card into the STM32MP135F-DK and power on the board.

The resulting behavior depends on the image configuration and the board's boot settings.

## Building from Source

The complete build environment is available in the source archive.

### 1. Download and extract the archive

```bash
tar -xzvf sauvegarde_yocto_complet.tar.gz
```

Navigate to the extracted project directory. The exact path depends on the archive's structure.

### 2. Initialize the Yocto environment

Navigate to the Poky directory:

```bash
cd poky
source oe-init-build-env
```

This command initializes the build environment and normally changes the current directory to the build directory.

### 3. Verify the configuration

Before compiling, verify that:

- The appropriate machine configuration is selected.
- The custom layer is included in `bblayers.conf`.
- The required settings are present in `local.conf`.
- All required layers and dependencies are available.

### 4. Build the image

```bash
bitbake core-image-ubs-homeautomation
```

BitBake resolves the dependencies and executes the tasks required to build the image.

**Note:** Yocto builds can require substantial disk space, memory, and compilation time.

## Security Considerations

Security is an important consideration when designing embedded Linux systems.

## Briques de Sécurité Implémentées
- **Secure Boot** : Mise en place de la chaîne de confiance et vérification d'intégrité du firmware avant le démarrage du noyau.
- **Isolation des Services (Docker)** : Conteneurisation des applicatifs domotiques avec gestion stricte des `namespaces` et `cgroups`.
- **Contrôle d'Accès Réseau & Système (AppArmor)** : Création de profils de sécurité sur-mesure pour appliquer le principe du moindre privilège aux services conteneurisé


### Remote Software Updates

The project uses OPKG for package management and HTTP for package delivery.

HTTP alone does not provide transport confidentiality or server authentication. The integrity and authenticity of packages must be assessed separately.

Potential security measures include authenticated package repositories, cryptographic package signature verification, and secure transport.


The mechanisms actually implemented and tested should be documented with their corresponding configuration and evidence.


## Author

**Meysoun Gharbi**

Embedded Systems Cybersecurity Student  
Université Bretagne Sud, France
>>>>>>> 87f3a0f2a4e67bbff9d6aa2720360430161e7de7
