# STM32 Kernel IAP – Bootloader & Linux Driver Based Firmware Update System

## 📌 Project Overview

This project demonstrates a **complete embedded firmware update pipeline** using an STM32 microcontroller and a Linux host system. It combines **bare‑metal embedded programming**, a **custom bootloader with In‑Application Programming (IAP)**, and a **Linux kernel character driver** to perform firmware updates over UART.

The embedded application implements a **Traffic Signal Controller**, while the bootloader enables safe firmware upgrades without external programmers. The Linux kernel driver acts as the host‑side interface for communicating with the STM32 device.

This project is designed to showcase **real‑world embedded systems concepts** used in production devices such as IoT nodes, industrial controllers, and consumer electronics.

---

## 🧠 Key Concepts Demonstrated

* STM32 **custom bootloader** design
* **In‑Application Programming (IAP)** using internal Flash
* Flash memory partitioning (Bootloader vs Application)
* UART‑based firmware transfer protocol
* Linux **kernel character device driver**
* Host ↔ MCU communication
* Bare‑metal and **RTOS embeddec C programming**
* System‑level thinking across OS and hardware

---

## 🏗️ System Architecture

```
+-------------------+        UART       +-------------------------+
|   Linux System    | <---------------> |        STM32 MCU        |
|                   |                   |                         |
|  Kernel Driver    |                   |  Bootloader (IAP)       |
| (/dev/stm32_ctrl) |                   |  Application Firmware   |
| (/dev/ttyACM0)    |                   |  Traffic Controller     |
+-------------------+                   +-------------------------+
```

## 🔌 Hardware Requirements

* **STM32 Blue Pill (STM32F103C8)** or compatible STM32 MCU
* USB‑to‑UART adapter (FTDI / CP2102)
* LEDs (Red, Yellow, Green) + resistors (for traffic controller demo)
* Linux PC (tested on Ubuntu) / Virtual Machine

### UART Connections

| STM32 Pin | USB‑UART |
| --------- | -------- |
| PA9 (TX)  | RX       |
| PA10 (RX) | TX       |
| GND       | GND      |

---

## 🧰 Software & Tools

### Embedded Side

* STM32CubeIDE / arm‑none‑eabi‑gcc
* OpenOCD / ST‑Link
* C (bare‑metal)

### Linux Side

* Linux kernel headers
* GCC
* Makefile

---

## 🧪 Traffic Signal Application

* Implements a basic **traffic light controller** using GPIOs
* Uses timed state transitions (RED → YELLOW → GREEN)
* Can be **updated remotely** using the IAP mechanism

---

## 🐧 Linux Kernel Driver

### Driver Features

* Character device interface
* UART communication with STM32
* Sends firmware binary in chunks
* Triggers bootloader update mode

## 🔄 Firmware Update Flow (IAP)

1. Linux driver sends update command
2. STM32 jumps to bootloader
3. Bootloader erases application flash region
4. New firmware is received over UART
5. Flash programming is performed safely
6. Bootloader jumps to updated application

---
## 🚀 Why This Project Matters

This project reflects **industry‑relevant embedded development**, including:

* Firmware update mechanisms used in real devices
* Bootloader design considerations
* Cross‑domain development (Linux + MCU)
* Low‑level debugging and flash management

It goes beyond basic microcontroller examples and demonstrates **system‑level embedded engineering skills**.

---

## 📈 Possible Improvements

* CRC / checksum verification for firmware
* Flash encryption or authentication
* Support for multiple STM32 families
* CLI user‑space tool instead of kernel driver
