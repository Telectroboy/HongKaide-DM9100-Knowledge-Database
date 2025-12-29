# HONGKAIDE / ZASTONE DM9100  
**VHF / UHF Transceiver – Knowledge Database**

![Status](https://img.shields.io/badge/status-active-success)
![Band](https://img.shields.io/badge/band-VHF%20%2F%20UHF-blue)
![License](https://img.shields.io/badge/license-Documentation-lightgrey)

> Centralized technical documentation, calibration references, and reverse-engineering notes  
> for the **HONGKAIDE / ZASTONE DM9100** transceiver.

---

## Table of Contents
- [Overview](#overview)
- [Frequency Coverage](#frequency-coverage)
- [RF Output Power](#rf-output-power)
- [Hardware Architecture](#hardware-architecture)
- [RF Power Amplifiers](#rf-power-amplifiers)
- [Calibration](#calibration)
- [Test Mode](#test-mode)
- [Compatibility](#compatibility)
- [Media & Reviews](#media--reviews)
- [Repository Structure](#repository-structure)
- [Known Limitations](#known-limitations)
- [Disclaimer](#disclaimer)

---

## Overview

- **Manufacturer / Brand names:** **HONGKAIDE / ZASTONE**
- **Device type:** DMR / Analog VHF-UHF transceiver
- **Target audience:** Radio amateurs, maintainers, reverse-engineering enthusiasts

---

## Frequency Coverage

| Band | RX | TX |
|-----|----|----|
| **VHF** | 136–174 MHz | **144–148 MHz only** |
| **UHF** | 400–470 MHz | **430–440 MHz only** |

⚠️ TX ranges are **hardware / firmware limited** to amateur radio bands.

---

## RF Output Power

- **Low:** 5 W  
- **Mid:** 14 W  
- **High:** 25–30 W *(depends on calibration and individual unit)*

---

## Hardware Architecture

### Main ICs

- **Main MCU:** **STM32F405**
- **DMR baseband:** **HR_C6000**
- **Analog RF:** **AT1846S**
- **FM broadcast / AM aviation RX:** **BK1088**
- **GPS module:** **ATGM336M** *(external antenna)*

---

## RF Power Amplifiers

- **Driver / Pre-PA:** **RD08MUS2**  
  Datasheet:  
  https://www.mitsubishielectric.com/semiconductors/hf/products/datasheet/rd08mus2.pdf

- **Final PA stage:** **RD35HUP2**  
  Datasheet:  
  https://www.mitsubishielectric.com/semiconductors/hf/products/datasheet/rd35hup2.pdf

---

## Calibration

### 2024 models – reference values

⚠️ **CRITICAL WARNING**  
**ALWAYS BACKUP YOUR ORIGINAL CALIBRATION VALUES.**  
Each transceiver is factory fine-tuned and **values differ from unit to unit**.

![DM9100 calibration values – 2024](Pictures/DM9100calibValuesEN.png)

Direct link:  
https://github.com/Telectroboy/HongKaide-DM9100-Knowledge-Database/blob/main/Pictures/DM9100calibValuesEN.png

### 2025 models

⚠️ **Different calibration set than 2024 models.**  
**Do NOT reuse 2024 values.**  
**USE AT YOUR OWN RISK.**

---

## Test Mode

**Known test mode passwords:**

- `D9000T`
- `D9000P`

---

## Compatibility

- **OpenGD77:** ❌ **Not compatible**
- **Firmware cross-flashing:** ❌ Not recommended
- **Calibration data sharing:** ⚠️ Unsafe between different units

---

## Media & Reviews

### Videos
- https://dzen.ru/video/watch/67307fc811758550851d9cd2  
- https://www.youtube.com/watch?v=2Z-eE43wDwA  
- https://www.youtube.com/watch?v=r8jfXZ_tVko  

### Review
- https://viva-telecom.org/17119/zastone/d9100/review/

---

## Repository Structure

```text
.
├─ README.md
├─ Pictures/
│  └─ DM9100calibValuesEN.png
├─ Docs/
│  ├─ calibration.md
│  ├─ hardware.md
│  └─ notes.md
└─ Firmware/
   └─ (if applicable)



Knowledge DataBase about VHF UHF Transceiver HONGKAIDE DM9100

HONGKAIDE or ZASTONE manufacturer

VHF receive frequence 136 to 174MHz  TX frequency Range 144 to 148MHz ONLY
UHF RX frequency range 400 to 470MHz TX Frequence Range 430 to 440Mhz ONLY

POWER :
Low = 5W
Mid = 14W
High = 25 to 30W

Main MCU => STM32F405
DMR chip = HR_C6000
Analog Chip = AT1846S
FM broadcast and AM aviation RX Chip = BK1088 
GPS Chip is ATGM336M with external antenna
RD08MUS2 1st of Power Amplifier https://www.mitsubishielectric.com/semiconductors/hf/products/datasheet/rd08mus2.pdf
RD35HUP2 Power Amplifier last stage https://www.mitsubishielectric.com/semiconductors/hf/products/datasheet/rd35hup2.pdf





Not compatible with OpenGD77


Videos :
https://dzen.ru/video/watch/67307fc811758550851d9cd2 or https://www.youtube.com/watch?v=2Z-eE43wDwA

https://www.youtube.com/watch?v=r8jfXZ_tVko

Review :
https://viva-telecom.org/17119/zastone/d9100/review/

Standardized set of values for 2024 model DM9100
https://github.com/Telectroboy/HongKaide-DM9100-Knowledge-Database/blob/main/Pictures/DM9100calibValuesEN.png
SAVE YOUR VALUES before doing anything else!!! 
Each transceiver has different values according to fine tuning calibration.

2025 transceiver have another set of values !
USE AT YOUR OWN RISK.

Test mode password = D9000T or D9000P







