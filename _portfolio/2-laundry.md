---
title: "Smart Laundry Payment Module — IoT-Based Commercial Control Board"
excerpt: "Developed a commercial IoT control board enabling washing machines only after successful payment via mobile banking app (Kaspi Bank). <br/> ![smart_laundry](https://raw.githubusercontent.com/alibeknakhimov/Smart-Laundry-Payment-Module/refs/heads/main/media/photos/block-diagram.png)"
collection: portfolio
---

The **Smart Laundry Payment Module** is a **production-grade IoT control system** designed and deployed for laundromats across Kazakhstan.  
It enables **cashless machine activation** via mobile banking apps (e.g., **Kaspi Bank**) and ensures complete appliance safety through **optically isolated high-voltage control**.

The project combines **custom PCB design**, **embedded firmware**, and **secure backend communication**, forming a scalable commercial solution for contactless laundry automation.

---

### Highlights

- **Custom PCB Design:**  
  Designed a **CNC-milled control board** based on **ESP-12F (ESP8266)**, featuring **three PC817 optocouplers**, an **AMS1117-3.3V regulator**, and high-voltage isolation for 230V appliance control.  
  Provided complete **hardware package** — schematics, PCB, Gerber, and BOM.

- **Firmware Implementation:**  
  Developed **state-machine-based firmware**, handling Wi-Fi setup, secure HTTP polling, and machine control logic.  
  The firmware interacts with a **custom backend API**, checking payment status and triggering appliance start sequences.

- **Backend Integration:**  
  The system periodically polls a **cloud server** over HTTP to verify payment confirmations and uses secure logic to handle concurrent user access.

- **System Safety:**  
  Implemented **opto-isolated control** between logic and power sections, ensuring user safety and appliance reliability.

- **Ease of Deployment:**  
  Designed for **quick installation** and **plug-and-play retrofit** into existing laundromat systems with clear wiring, setup, and deployment documentation.

---

### Tools & Technologies

**Hardware:** ESP-12F (ESP8266), AMS1117-3.3V, PC817  
**Software:** PlatfromIO, HTTP client logic, custom API   
**Concepts:** IoT device integration, optocoupler-based isolation, HTTP polling automation  

---

### Deployment

The system has been **commercially deployed** across multiple laundromats, allowing customers to activate washing machines using mobile payments.  
This innovation eliminated the need for coin or token systems and significantly simplified maintenance and monitoring for operators.

---

### System in Action

<iframe width="560" height="315" src="https://www.youtube.com/embed/Hlpey2L98C8?si=zOXEdxtHM5OTurdS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
