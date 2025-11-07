---
title: "Qt STM32 Ethernet Control System"
excerpt: "Developed a full-stack embedded system combining a Qt5 touchscreen GUI on Raspberry Pi and a bare-metal STM32 microcontroller communicating over Ethernet via a CH9121 bridge. <br/> ![qt_stm32_ethernet](https://raw.githubusercontent.com/alibeknakhimov/qt-stm32-ethernet-control/refs/heads/main/media/thumbnail.png)"
collection: portfolio
---

This project, **Qt STM32 Ethernet Control**, demonstrates a complete end-to-end embedded system integrating a Qt5 graphical interface running on a Raspberry Pi 5 with a bare-metal STM32F103 microcontroller, communicating over Ethernet through a CH9121 ETH2UART bridge.

The objective was to achieve real-time remote hardware control (relay modules) via a modern GUI, combining low-level embedded programming, TCP networking, and embedded Linux integration — all without using STM32 HAL, CMSIS, or desktop environments.

---

### System Overview

The architecture features three core components:

1. **Qt5 GUI Application (Raspberry Pi 5):**  
   - Acts as a TCP server listening on port `2000`.  
   - Built with C++/QML, optimized for touchscreen interaction via EGLFS (no X11/Wayland).  
   - User actions (e.g., slider movements) send UTF-8 encoded commands over TCP.  
   - Includes systemd startup scripts and a custom splash screen for appliance-style operation.  

2. **STM32 Bare-Metal Firmware:**  
   - Written entirely in C using direct register access (no HAL).  
   - Handles UART communication and GPIO relay control.  
   - Parses UTF-8 commands received via CH9121 to toggle or set relay states.  
   - Includes setup routines for configuring the CH9121’s network parameters.  

3. **CH9121 Bridge:**  
   - Serves as a transparent Ethernet-to-UART converter, bridging TCP messages from Qt to STM32.  
   - Configured dynamically by the STM32 during initialization.  

---

### Highlights

- **Bare-Metal Firmware:** 100% register-level STM32 programming.  
- **Qt Touchscreen Interface:** Lightweight and smooth GUI on Raspberry Pi using EGLFS.  
- **Real-Time TCP Communication:** Instant relay actuation via CH9121 bridge.  
- **Systemd Integration:** Autostart, headless boot, and splash logo for seamless use.  
- **Cross-Platform Simplicity:** UTF-8 protocol compatible with any TCP client.  

---

### Hardware Used

- **Raspberry Pi 5** – runs Qt GUI and TCP server  
- **STM32F103C8T6 (Blue Pill)** – bare-metal firmware  
- **CH9121 ETH2UART Bridge** – TCP-UART bridge  
- **Relay Module** – hardware output controlled by STM32  

---

### Demonstration Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/yx8_98DvfXI?si=I2OE2vgGjDrKH_mC&amp;start=71" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---


