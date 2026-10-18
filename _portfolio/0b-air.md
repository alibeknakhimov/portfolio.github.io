---
title: "Air Quality Payload on a VTOL Aircraft"
excerpt: "Put a Jetson Orin Nano and two sensor boards into the cargo bay of a VTOL, and brought SSH plus MAVLink up over the existing radio. <br/> ![air quality sensor head](https://raw.githubusercontent.com/alibeknakhimov/alibeknakhimov.github.io/refs/heads/main/images/air/02_sensor_head.jpg)"
collection: portfolio
redirect_from:
  - /portfolio/0a-air/
---

University research project under a state targeted-funding program. The aircraft is a VTOL hybrid that climbs vertically and then cruises as a plane.

**Role:** I did the payload alone. Companion computer, cargo-bay connector, and both sensor boards.  
**Timeline:** about 2 months.  
**Computer:** NVIDIA Jetson Orin Nano.

---

### The aircraft

![VTOL on the bench, cargo bay open]({{ site.url }}{{ site.baseurl }}/images/air/01_aircraft.jpg)

The cargo bay slides out of the fuselage on latches. A plug on that bay already carried power. I added Ethernet to the same plug, so the payload seats as one module: push it in, and power and the network connect together.

---

### What is in the bay

Jetson Orin Nano, two sensor boards, and two STM32 Blackpill boards. Pull the bay and the whole payload comes out.

Where each part sits, and how the bay slides in on the rails.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/air/bay.mp4" type="video/mp4">
</video>

<img src="{{ site.url }}{{ site.baseurl }}/images/air/04_bay.jpg" alt="Wiring inside the cargo bay" style="width:860px;max-width:100%;height:auto;">

![Bench setup: aircraft, handheld transmitter, ground station]({{ site.url }}{{ site.baseurl }}/images/air/05_bench.jpg)

---

### Link

The radio uses the same bands as a cellular network. The handheld transmitter is one end. The receiver on the aircraft is the other, and it has an Ethernet connector the manual never really explained. I took those pins out, soldered them, and landed Ethernet and power on the cargo-bay plug, then into the Jetson.

SSH came up on that interface, so the Jetson console was reachable from the ground and scripts could be started on the aircraft. The same Ethernet already carried MAVLink, so vehicle telemetry and the payload console share one network.

<pre class="mermaid">
flowchart LR
    GS[Ground station] -->|cellular-band radio| RX[Onboard receiver]
    RX -->|Ethernet on the cargo plug| JET[Jetson Orin Nano]
    PLUG[Cargo plug power] --> JET
    JET -->|USB| BP1[STM32 Blackpill]
    JET -->|USB| BP2[STM32 Blackpill]
    BP1 -->|short I2C| AQ[SCD30, SPS30, BMP388, BME280]
    BP2 -->|short I2C| GAS[CO, NO2, O3, SO2 cells]
</pre>

<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
</script>

---

### Sensors

Two boards.

The air board speaks I2C: Sensirion SCD30 for CO2, Sensirion SPS30 for particulates, Bosch BMP388 and BME280 for pressure, temperature, and humidity.

The gas board carries four Alphasense electrochemical cells: CO-B4, NO2-B43F, OX-B431 for ozone, and SO2-B4. The cells themselves are analog. On this board they are read over I2C.

![Four gas cells, SCD30 and SPS30]({{ site.url }}{{ site.baseurl }}/images/air/02_sensor_head.jpg)

---

### Why two microcontrollers

Both boards on one I2C bus into the Jetson produced heavy noise and the samples came out distorted. I2C does not survive a long run next to motors and a radio in a cellular band, and the Jetson controller is a poor host for that bus.

Each board now has its own Blackpill. The MCU reads its sensors on a short I2C bus and sends the samples to the Jetson over USB. Both boards take power from the Jetson.

A Blackpill has a diode so that an external 5 V supply cannot feed back into the USB connector. I removed that diode. Each board has a single supply, the Jetson USB port, so there is no second source to feed back, and the 5 V rail is not dropped across the diode.

---

### One logged run

![CO2, particulates, and the four gas cells over 20 minutes]({{ site.url }}{{ site.baseurl }}/images/air/06_run.png)

The clock in the log was stuck in year 2000. Spacing between samples is unchanged, and the first sample is placed at 14:07 on 6 May 2026.

CO2 from the SCD30 rose from about 230 ppm and leveled near 630 ppm. PM2.5 from the SPS30 stayed near 16 µg/m³. CO, NO2, OX, and SO2 are each on their own scale. The file does not name their units. OX sits below zero for most of the run, so that zero is not a finished calibration.

---

### In flight

Ground station during the flight: attitude, altitude, and the track. Then the aircraft in cruise.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/air/flight.mp4" type="video/mp4">
</video>

---

### Tools

**Computer:** NVIDIA Jetson Orin Nano  
**MCUs:** two STM32 Blackpill boards, I2C in, USB out  
**Link:** Ethernet on the cargo-bay plug, SSH and MAVLink over the existing radio  
**Sensors:** SCD30, SPS30, BMP388, BME280, Alphasense CO-B4, NO2-B43F, OX-B431, SO2-B4
