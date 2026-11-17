# Custom Betaflight Brushed Flight Controller

## Overview

This project is a custom-designed, 4-layer brushed quadcopter flight controller built for Betaflight. It integrates an STM32F411 microcontroller, a BMI088 IMU, and a CC2500 2.4 GHz radio receiver into a highly compact form factor. The board is fully optimized for JLCPCB's manufacturing and assembly services.

## Background & Design Process

This project began with reverse-engineering an existing commercial drone. By taking apart the original flight controller, I analyzed how it handled power distribution and communicated with the brushed DC motors. Once the battery constraints, motor control, and chip interactions were understood, I began selecting custom components:

*   **MCU Selection & Setup:** After researching custom FPV drone builds, I selected the STM32F411CEU6 for its processing performance and native support for Betaflight and USB-C flashing. 
*   **Power & Motor Control:** A low-dropout (LDO) regulator was selected to step down the battery voltage to a stable 3.3V. For motor control, the MCU pins drive MOSFETs using pulse-width modulation (PWM) to control the power sent to the motors, achieving the desired spin speed.
*   **IMU Integration:** The BMI088 IMU was implemented following the manufacturer's recommended SPI protocol guidelines. Only the Gyro interrupt was wired, as this is the only interrupt required for the flight loop.
*   **Radio & RF Stage:** The CC2500 2.4 GHz transceiver handles radio communication. Crystal and decoupling guidelines were followed from the datasheet, but instead of using a discrete reference balun, a premade matched balun from Johanson Technology was implemented to convert the unbalanced signal to the antenna for transmission.

## Hardware Specifications

* **MCU:** STM32F411
* **IMU:** BMI088
* **Radio:** CC2500 2.4 GHz RF Stage (Tuned 50-ohm impedance)
* **Motor Drivers:** 3-Amp brushed motor driver stage using AO3400A MOSFETs
* **USB Interface:** USB-C (0.5mm pitch)
* **Power:** 3.3V LDO circuitry with heavy copper links for power tracks


## PCB Design & Layout Notes

Designed in KiCad with a strict zero-DRC error standard.

* **Stackup:** 4-Layer board.
* **Grounding:** Extensive ground stitching, MCU central ground anchoring (4 vias), and RF perimeter vias to ensure signal integrity.
* **Zone Hierarchy:** Global `F.Cu` `GND` pour set to priority `0`. Localized high-current zones (`+BATT`, `3.3V`) set to priority `1+` and configured as **Solid** to satisfy min-spoke and connection requirements.
* **Clearances:** Custom footprint-level clearance adjustments applied to the USB-C connector and the battery connector (`J7`) to handle tight built-in spacing and unplated mounting hole limits.

## Manufacturing (JLCPCB)

This board is designed to be manufactured and partially assembled (PCBA) by JLCPCB.

**Ordering Parameters:**
* **Layers:** 4
* **Impedance Control:** Yes
* **Stackup:** JLC04161H-3313 (Crucial for the 50-ohm RF trace)
* **Assembly Type:** Economic PCBA

**Exporting Files:**
Manufacturing files are generated using the KiCad Fabrication Toolkit plugin, which automatically exports the Gerbers, BOM, and CPL (Pick and Place) files required for JLCPCB's automated quoting engine.


### 1. Factory Assembly (JLCPCB PCBA)
The following surface-mount components are handled by JLCPCB:
* STM32F411 MCU
* BMI088 IMU (LGA/Hidden pads)
* CC2500 Radio (QFN/Hidden pads)
* USB-C Connector (Critical due to 0.5mm pitch inner pins)
* AO3400A MOSFETs, LDOs, diodes, and all 0402/0603 passive components.

