---
title: "Oilfield Pump-Jack Cabinet: LoRaWAN Beside the Existing PLC"
excerpt: "Oilfield work. I designed the LoRaWAN board for a pump-jack cabinet and took part in the integration and the field tests. A student I was teaching wrote the firmware and is carrying the project on. The node reads the PLC that already runs the pump and writes the speed that program uses. <br/> ![pump-jack cabinet node](https://raw.githubusercontent.com/alibeknakhimov/alibeknakhimov.github.io/refs/heads/main/images/well/01_board.jpg)"
collection: portfolio
---

This is an oilfield pump jack. The cabinet already had drives, contactors, and a PLC with its own pumping program. I designed the LoRaWAN board, and I took part in the integration and the field tests. The firmware was written by a student I was teaching. He is continuing the project. The silk also carries the mark of the university where he studies.

The node reads the PLC over Modbus and writes the speed into variables the program already uses. Pumping stays in the PLC. If the radio drops, the cabinet keeps running.

A board from another company had been in the cabinet. It was 12 V only, it had no documentation, and it failed on the cabinet's 24 V feed. This board takes that 24 V on its own converter, so the cabinet no longer needs a separate supply.

The board charges an external battery. In a mains outage the battery carries the node. The node stays low power, reports the loss of power, and spare relays can switch a load for that outage or for a fault. It is a LoRaWAN Class A device. In the capture below the uplinks are about a minute apart, and the frame counter is already past 4200.

On the site the link was checked at about 400 m, 900 m, 2 km, and 5 km.

![Node on the bench, antenna and relay outputs fitted]({{ site.url }}{{ site.baseurl }}/images/well/01_board.jpg)

![Same node in the oilfield cabinet, on the 24 V feed beside the existing drives]({{ site.url }}{{ site.baseurl }}/images/well/02_cabinet.jpg)

---

### What the operator sees

The screen is ChirpStack. With the device keys on the server, ChirpStack decrypts the uplink. The event row still shows the payload as Base64. That is the byte string, not a sentence. A codec on the device profile turns those bytes into the Modbus registers.

The browser bar is covered. In the event detail, the gateway coordinates are covered. The Base64 line and the decoded registers stay in the frame.

<video controls playsinline preload="metadata" width="100%" style="max-width:480px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/well/uplink.mp4?v=2" type="video/mp4">
</video>

---

### Path

<pre class="mermaid">
flowchart LR
    FEED[Cabinet 24 V] --> NODE[LoRaWAN node]
    NODE -->|charges| BAT[External battery]
    BAT -->|mains outage| NODE
    NODE -->|Modbus read and speed write| PLC[Existing PLC program]
    PLC --> DRIVE[Drives already in the cabinet]
    NODE -->|Class A uplink| GW[Gateway]
    GW --> CS[ChirpStack]
</pre>

<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
</script>
