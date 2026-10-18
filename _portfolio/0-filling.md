---
title: "Gravimetric Filling Line: Firmware Modernization"
excerpt: "Modernized a production pharmaceutical filling line: replaced a blocking HX711 weighing board with Modbus RTU indicators, rewrote the STM32 stepper firmware, and raised throughput from 480 to 650 bottles per hour. <br/> ![gravimetric filling line](https://raw.githubusercontent.com/alibeknakhimov/alibeknakhimov.github.io/refs/heads/main/images/filling/01_machine_overview.jpg)"
collection: portfolio
---

Brownfield modernization of a **5-column gravimetric filling line** for liquid pharmaceutical product. The mechanics, drum, nozzle axes, peristaltic pumps, and the stepper carrier board stayed. I replaced the weighing chain, the link to the operator PC, and the fill-control firmware.

**Result: 480 to 650 bottles per hour (+35%).** Calibration moved from a hidden menu inside the enclosure to the buttons on each weighing indicator.

**Role:** sole embedded engineer (firmware, RS-485 to UART shield, on-site integration).  
**Timeline:** 3 weeks on a dedicated test rig, 2 days of integration, 3 months of field iterations in production.  
**MCU:** STM32F407VG, bare-metal plus HAL, STM32CubeIDE.

---

### The line

![Five-column filling line]({{ site.url }}{{ site.baseurl }}/images/filling/01_machine_overview.jpg)

A rotating drum presents bottles to five nozzle columns. Each column has a peristaltic pump on a stepper, a nozzle on a vertical stepper, and a load cell under the bottle. One MCU on the stepper board drives every motor through a Darlington array into industrial step/dir drivers.

---

### What changed

| | 2021 system | After modernization |
|---|---|---|
| Weighing | Custom PCB, 4x HX711, bit-banged on STM32F1 | 5x QLST indicators, Modbus RTU, addresses 1 to 5, 115200 bps |
| Weighing MCU | Dedicated board, blocking main loop | Removed |
| Link to the stepper board | UART on the ribbon between the two boards | Same UART pins, now into my RS-485 shield |
| Calibration | 16x2 LCD inside the enclosure | Buttons and display on each indicator |
| Adapter | Ribbon into the stepper board | Shield plugs into that same connector, stepper PCB unchanged |
| HMI | Long UART cable to a Raspberry Pi | USB CDC |
| Firmware | Entire application inside one TIM6 interrupt | Drum on TIM7 (priority 0), nozzles and pumps on TIM6 (priority 1), Modbus state machine in the main loop |
| Fill control | Bang-bang on two thresholds | Soft start, fast fill, nozzle follows the liquid surface, slow finish, drip tail |
| Throughput | 480 bottles/hour | 650 bottles/hour |

---

### Architecture

**Before.** Four HX711 channels were bit-banged in a blocking loop on the weighing board. That board sent weights to the stepper board over UART on the ribbon. The stepper MCU did drum, nozzles, pumps, and sensing inside one 10 kHz interrupt.

<pre class="mermaid">
flowchart LR
    LC[4 load cells] --> HX[STM32F1 and 4x HX711]
    HX -->|UART on the ribbon| MCU[STM32F4 TIM6 ISR]
    MCU --> DRV[Step drivers]
    DRV --> MECH[Drum, nozzles, pumps]
    PI[Raspberry Pi HMI] -->|long UART| MCU
</pre>

**After.** The weighing board is gone. Five indicators sit on one RS-485 bus. The shield plugs into the same ribbon connector on the stepper board and uses the UART pins that used to talk to the weighing board. The stepper PCB was not respun.

<pre class="mermaid">
flowchart LR
    LC[5 load cells] --> Q[5 QLST indicators]
    Q -->|RS-485 115200| SH[RS-485 to UART shield]
    SH -->|same UART pins| MCU[STM32F4]
    MCU --> TIM[TIM6 nozzles and pumps]
    MCU --> DRUMIRQ[TIM7 drum stepper]
    MCU --> MB[Modbus master]
    TIM --> DRV[Step drivers]
    DRUMIRQ --> DRV
    DRV --> MECH[Drum, nozzles, pumps]
    PI[Raspberry Pi HMI] -->|USB CDC| MCU
</pre>

<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
  mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
</script>

---

### Fill algorithm

The old control was two comparisons: pump on below `target - 10 g`, pump off at `target - 1.5 g`. Splash on the bottle neck forced a long dwell before the drum could index.

The new profile is scaled from a 93 g reference dose, so the same shape runs at 50 g and 93 g:

1. **Soft start** at the bottom of the bottle, until the surface rises.
2. **Fast fill** through the body.
3. **Nozzle tracks mass.** It stays low, then rises in proportion to the measured weight so the stream never free-falls more than a few millimetres.
4. **Slow finish and drip tail**, then a hard stop.
5. **Per-column speed trim** from the mass delta between Modbus sweeps, so the slowest pump does not set the cycle time.
6. **Interlocks:** dose latch, pause a column if its scale goes stale for 400 ms, abort the fill after 45 s.

---

### In motion

A longer run of the line.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/filling/fill_cycle.mp4" type="video/mp4">
</video>

One bottle: nozzle down, soft start, fast fill, slow finish, nozzle up.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/filling/single_fill.mp4" type="video/mp4">
</video>

---

### Hardware I added

The shield is a protoboard that plugs into the ribbon connector on the stepper board. That ribbon already carried UART between the stepper board and the weighing board. Those UART pins now go into a MAX485-class transceiver, and from there to the QLST bus. The stepper PCB was not modified.

| Shield | QLST indicator | Bottle on the drum |
| :---: | :---: | :---: |
| ![RS-485 to UART shield]({{ site.url }}{{ site.baseurl }}/images/filling/02_shield_top.jpg) | ![QLST weighing indicator]({{ site.url }}{{ site.baseurl }}/images/filling/04_qlst_indicator.jpg) | ![Bottle seated on the drum carrier]({{ site.url }}{{ site.baseurl }}/images/filling/06_field_closeup.jpg) |

Modbus master: non-blocking state machine, 35 ms between polls, 25 ms response timeout, CRC-16, then the next slave. One silent indicator does not stall the other four. Register `0x11` is used for peeling (runtime tare). Register `0x12` rewrites the EEPROM zero and is not touched at runtime.

---

### Field work

Three months on the running line, not only the bench:

- A first attempt kept the weighing board. One of its four HX711 chips sat on two pins that could also be UART, so that chip was removed and those pins were wired to the new indicators. It ran, with jitter, delay, and overfill, because the rest of that board was still bit-banging the other HX711s. The weighing board was then removed, and the shield took its place on the existing UART ribbon.
- A fifth column stretched the poll time. Bus speed went to 115200 and the indicators to a 40 Hz update.
- Reused loom connectors corroded. The harness was rebuilt with ferrules, snap connectors, a twisted pair, and the drain bonded to the PG pin on each indicator.
- The nozzle was touching the bottle, and the load cell was grazing the drum plate. Both put false mass on the scale. A firmware pre-arm gate now waits for real weight and for the nozzle to be down.

---

### Tools

**MCU:** STM32F407VG, TIM6, TIM7, USART2, USB CDC  
**Bus:** Modbus RTU, RS-485, QLST indicators  
**Tools:** STM32CubeIDE, STM32CubeMX, HAL  
**Kept from the 2021 machine:** mechanics, stepper carrier board, Darlington array, industrial step drivers

---

### GitHub

[Project on GitHub](https://github.com/alibeknakhimov/gravimetric-filling-line)
