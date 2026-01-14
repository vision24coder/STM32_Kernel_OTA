# Multi-Tasking Traffic Controller with In-Application Programming (IAP)

A full-stack embedded Linux project featuring an STM32-based Traffic Signal Controller. This system utilizes a custom **Linux Character Driver** to manage communication between a host PC and an STM32 Blue Pill, supporting both real-time traffic tasks and **In-Application Programming (IAP)** for seamless firmware updates over UART.

## 🚀 Project Overview

The system is architected into three distinct layers to handle real-time control and memory management:

* **Application Layer (STM32):** A multi-tasking traffic signal system.
    * **Task 1 (Auto-Mode):** Simultaneous blinking and cycling of Red, Yellow, and Green LEDs.
    * **Task 2 (Manual Override):** Real-time interruption via UART commands to force specific signal states.
* **Bootloader Layer (STM32):** A custom secondary bootloader residing in the first sector of Flash memory. It manages the handshaking protocol, sector erasing, and high-speed programming of incoming `.bin` files using IAP techniques.
* **Kernel Layer (Linux):** A specialized Character Device Driver acting as a UART wrapper. It provides a robust interface for user-space applications to interact with the hardware through a circular buffer and custom `ioctl` commands.

---

## 🛠️ Technical Specifications

### STM32 Memory Segmentation
| Region | Memory Address | Purpose |
| :--- | :--- | :--- |
| **Bootloader** | `0x08000000` | UART Handshaking & Flash Programming |
| **Application** | `0x08008000` | Traffic Logic & Manual Override Tasks |

### Linux Character Driver (`stm32_ctrl`)
The driver abstracts raw UART registers into a standard Linux file interface:
* **Circular Buffer:** Implemented in the `read` function to prevent data loss during high-speed firmware streaming.
* **IOCTL Interface:** * `SET_BAUD`: Dynamically adjust communication speeds.
    * `CLR_BUF`: Flushes the kernel and hardware FIFO buffers to resolve synchronization and "ghost character" errors.
    * `ENTER_BOOT`: Software-triggered jump from Application mode to the Bootloader.
    * `GET_STATUS`: Polls the controller's current mode and health.

---

## 🚦 Application Logic (Traffic Signal)

In **Application Mode**, the system cycles through signal states automatically. Sending specific characters via the Python interface triggers a **Manual Override**:

| Command | Action |
| :--- | :--- |
| **'R'** | Forces **Red** Light ON |
| **'Y'** | Forces **Yellow** Light ON |
| **'G'** | Forces **Green** Light ON |
| **'E'** | Exits Manual Override and returns to **Auto-Cycle** |

---

## 🔧 Bootloader & IAP Protocol

To ensure reliability during firmware updates, the system follows a strict synchronization flow:
1.  **Handshake:** On Reset, the Bootloader opens a 5-second window to listen for an update trigger
2.  **Buffer Sync:** The driver executes `ioctl(CLR_BUF)` to ensure the UART pipe is clean before data transmission.
3.  **Fast Flash:** Data is transmitted in **256-byte chunks**. Each chunk is verified and written to the application space at `0x08008000`.
4.  **Verification:** The bootloader inspects the **Initial Stack Pointer (MSP)** of the new binary before jumping to ensure the application is valid.
