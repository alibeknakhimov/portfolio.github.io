---
title: "Breathalyzer Access Control Interface Board for Screening Tablet"
excerpt: "Designed a custom isolated interface PCB for a breathalyzer and completed full hardware integration including enclosure assembly, wiring, mounting, and prototype validation. <br/> ![breathalyzer_interface](https://raw.githubusercontent.com/alibeknakhimov/portfolio.github.io/refs/heads/master/images/tablet.jpg)"
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

- **Rapid Hardware Prototyping:**  
  Fabricated the first revision as a **CNC-milled FR-4 prototype**, enabling fast testing, electrical validation, and mechanical fitting before production iteration.

- **Full Device Assembly & Mechanical Integration:**  
  Assembled the tablet hardware, including internal wiring, power routing, connector layout, vibration-safe mounting, and enclosure fitment — ensuring a stable and serviceable final hardware stack.

- **Cross-Team Collaboration:**  
  Worked closely with the software development team to align hardware signals, timing assumptions, and interface expectations for seamless integration.

---

### Tools & Technologies

**Hardware:** Relay-based breathalyzer signals, SBC integration, optocouplers, regulated supply stage  
**Workflow:** Altium Designer, CNC milling, oscilloscope verification, bench testing  
**Concepts:** Galvanic isolation, signal conditioning, embedded systems interfacing  

---

### PCB

![](https://raw.githubusercontent.com/alibeknakhimov/portfolio.github.io/refs/heads/master/images/alc_pcb.jpg)

---

### Tablet in Action

<iframe width="560" height="315" src="https://www.youtube.com/embed/iK1eSbh4oyw?si=CKWtRpk2vZTpTtSe" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



