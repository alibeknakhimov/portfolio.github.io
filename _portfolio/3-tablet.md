---
title: "Breathalyzer Access Control Interface Board for Screening Tablet"
excerpt: "Designed a custom isolation and signaling interface allowing a breathalyzer to securely communicate access control states to a single-board computer. <br/> ![breathalyzer_interface](https://github.com/user-attachments/assets/example_board.jpg)"
collection: portfolio
---

This project involved integrating a commercial breathalyzer with a custom-built access control tablet used at facility entry points.  
The original device output high-voltage control signals intended for turnstile operation, requiring safe and reliable adaptation to low-voltage logic compatible with an SBC.

---

### Highlights

- **Isolation & Signal Conditioning:**  
  Designed a custom PCB featuring **optocoupler-based galvanic isolation** to safely convert high-voltage relay outputs from the breathalyzer into low-voltage logic inputs for the SBC.

- **State Mapping & Control Logic:**  
  Converted hardware signals into interpretable digital states such as:  
  ➤ *Ready for scan* · *User intoxicated* · *User clear* · *Device offline*  
  enabling the SBC to make automated access decisions.

- **Power Regulation & Safety:**  
  Implemented a power reduction stage to adapt industrial control voltages to safe MCU-level ranges while ensuring long-term reliability.

- **Rapid Prototyping:**  
  Fabricated the initial revision as a **CNC-milled FR-4 prototype**, enabling fast iteration, mechanical validation, and electrical testing prior to moving toward a production-ready version.

- **Mechanical Integration:**  
  Participated in enclosure integration and full device assembly, including wiring, mounting, and hardware validation.

- **Cross-Team Collaboration:**  
  Worked closely with software developers to align pin logic, timing behavior, and system requirements for reliable hardware–software interaction.

---

### Tools & Technologies

**Hardware:** Relay-based breathalyzer signals, SBC integration, optocouplers, regulated supply stage  
**Workflow:** Altium Designer, CNC milling, oscilloscope verification, bench testing  
**Concepts:** Galvanic isolation, signal conditioning, embedded systems interfacing  

---

### Deployment

The module is currently undergoing integration testing and is being prepared for deployment as part of a full entry-screening device.

---

