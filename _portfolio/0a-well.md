---
title: "Pump-Jack Cabinet: LoRaWAN Beside the Existing PLC"
excerpt: "Put a LoRaWAN node in a pump-jack cabinet. It reads the PLC that already runs the pump and writes the speed into variables that program uses. The radio is the link out, and the PLC still owns the pumping. <br/> ![pump-jack cabinet node](https://raw.githubusercontent.com/alibeknakhimov/alibeknakhimov.github.io/refs/heads/main/images/well/01_board.jpg)"
collection: portfolio
---

The pump jack already had a cabinet: drives, contactors, and a PLC with its own pumping program. I added a LoRaWAN node beside that program. The node reads the PLC over Modbus and writes the speed into variables the program already uses. Pumping stays in the PLC. If the radio drops, the cabinet keeps running.

The cabinet feed is 24 V. An earlier board accepted only 12 V and failed on that feed. This board takes the cabinet 24 V on its own converter, so the cabinet does not need a second supply.

An external battery carries the node through a mains outage. The board does not charge that battery. The node stays low power, reports the loss of power, and spare relays can switch a load for that outage or for a fault. It is a LoRaWAN Class A device. In the capture below the uplinks are about a minute apart, and the frame counter is already past 4200.

On the site the link was checked at about 400 m, 900 m, 2 km, and 5 km.

![Node on the bench, antenna and relay outputs fitted]({{ site.url }}{{ site.baseurl }}/images/well/01_board.jpg)

![Same node in the pump-jack cabinet, on the 24 V feed beside the existing drives]({{ site.url }}{{ site.baseurl }}/images/well/02_cabinet.jpg)

---

### What the operator sees

The screen is ChirpStack. With the device keys on the server, ChirpStack decrypts the uplink. The event row still shows the payload as Base64. That is the byte string, not a sentence. A codec on the device profile turns those bytes into the Modbus registers.

The browser bar is covered in this clip. The event detail is left out, because it carries the gateway position.

<video controls playsinline preload="metadata" width="100%" style="max-width:420px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/well/uplink.mp4" type="video/mp4">
</video>

---

### Path

<pre class="mermaid">
flowchart LR
    FEED[Cabinet 24 V] --> NODE[LoRaWAN node]
    BAT[External battery] --> NODE
    NODE -->|Modbus read and speed write| PLC[Existing PLC program]
    PLC --> DRIVE[Drives already in the cabinet]
    NODE -->|Class A uplink| GW[Gateway]
    GW --> CS[ChirpStack]
</pre>

<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
</script>
