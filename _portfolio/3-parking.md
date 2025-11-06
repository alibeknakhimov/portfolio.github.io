---
title: "Smart Parking Access System — IoT, AI & Cloud Integration"
excerpt: "Developed a fully automated parking barrier system using ESP32 IoT module, Firebase cloud, and YOLO-based vehicle tracking. <br/> ![smart_parking_cover](https://raw.githubusercontent.com/alibeknakhimov/Smart-Parking-Access/refs/heads/main/vision_service/processed_image_2.jpg)"
collection: portfolio
---

The **Smart Parking Access System** is a complete **IoT–AI solution** designed to automate parking gate control for university campuses and private facilities.  
It replaces outdated remote-control systems with **mobile app authorization**, **cloud-based command routing**, and **AI-powered vehicle monitoring**, achieving seamless and secure parking access management.

This system was **developed and deployed under AlmaValley**, integrating custom PCB hardware, secure Firebase communication, computer vision, and cross-platform user interfaces.

---

### Highlights

- **IoT Hardware Module (ESP32 + Custom PCB):**  
  Designed and built a **custom ESP32-based controller board** to receive secure open/close commands via Firebase Realtime Database.  
  Integrated **relay control circuitry**, **optical isolation**, and **failsafe manual override**.  

- **Cross-Platform App Ecosystem:**  
  Developed a **Flutter-based mobile app** for staff authentication and gate control.  
  Users can open the gate only if they are within a geofenced radius verified via **Firebase Authentication + Realtime Database**.

- **AI Vehicle Tracking (Python + YOLOv3):**  
  Deployed a **computer vision module** that analyzes CCTV feeds in real time to detect and log vehicle entry/exit events.  
  Utilized **YOLOv3** for detection and **ROI-based tracking** to determine direction of movement (entry or exit).

- **Security & Cloud Infrastructure:**  
  Implemented **secure authentication rules** in Firebase, verifying both user identity and location.  
  All transactions are **logged in real time**, providing a digital audit trail for access records.

- **Administrative Interface:**  
  Built a **desktop guard control panel (Flutter + Python)** to monitor gate activity, manage users, and export reports to Excel.

---

### Tools & Technologies

**Hardware:** ESP32, relay driver  
**Software:** Flutter (mobile + desktop), Firebase Realtime Database, Firebase Auth, Python, YOLOv3, PlatformIO 
**Design Tools:** Fusion 360
**Concepts:** IoT automation, AI-driven access control, secure cloud integration, geofencing  

---

### Deployment

The system is **fully operational** at the **AlmaValley parking site**, handling daily gate operations for staff and students.  
It has proven to be **reliable, secure, and scalable**, forming the foundation for future smart campus infrastructure.

---

|                      PCB                      |              Installed Enclosure               |
| :-------------------------------------------: | :--------------------------------------------: |
| ![](https://raw.githubusercontent.com/alibeknakhimov/Smart-Parking-Access/refs/heads/main/docs/hardware/pcb_prototype.jpg) | ![](https://raw.githubusercontent.com/alibeknakhimov/Smart-Parking-Access/refs/heads/main/docs/hardware/pcb_in_box.jpg) |
