---
title: "Gravimetric Filling Line: Firmware Modernization"
excerpt: "Modernized a production pharmaceutical filling line: replaced a blocking HX711 weighing board with Modbus RTU indicators, rewrote the STM32 stepper firmware, and raised throughput from 480 to 650 bottles per hour. <br/> ![gravimetric filling line](https://raw.githubusercontent.com/alibeknakhimov/portfolio.github.io/refs/heads/master/images/filling/01_machine_overview.jpg)"
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
| Weighing | Custom PCB, 5x HX711, bit-banged on STM32F1 | 5x QLST indicators, Modbus RTU, addresses 1 to 5, 115200 bps |
| Weighing MCU | Dedicated board, blocking main loop | Removed |
| Bus | Parallel ribbon | 2-wire RS-485, extra sensor by chaining |
| Calibration | 16x2 LCD inside the enclosure | Buttons and display on each indicator |
| Adapter | Ribbon into the stepper board | My RS-485 to UART shield, same connector, stepper PCB unchanged |
| HMI | Long UART cable to a Raspberry Pi | USB CDC |
| Firmware | Entire application inside one TIM6 interrupt | Motion tick in TIM6, drum event on its own EXTI, Modbus state machine in the main loop |
| Fill control | Bang-bang on two thresholds | Soft start, fast fill, nozzle follows the liquid surface, slow finish, drip tail |
| Throughput | 480 bottles/hour | 650 bottles/hour |

---

### Architecture

**Before.** Five HX711 channels were bit-banged in a blocking loop. The stepper MCU did drum, nozzles, pumps, and sensing inside one 10 kHz interrupt.

<div class="mermaid" markdown="0">
flowchart LR
    LC[5 x load cells] --> HX[STM32F1 plus 5 x HX711]
    HX -->|ribbon| MCU[STM32F4 TIM6 ISR]
    MCU --> DRV[Step drivers]
    DRV --> MECH[Drum, nozzles, pumps]
    PI[Raspberry Pi HMI] <-->|long UART| MCU
</div>

**After.** The weighing board is gone. Indicators sit on one RS-485 bus. The shield plugs into the original connector on the stepper board, so the carrier PCB was not respun. Pull the shield and the machine can return to the previous wiring.

<div class="mermaid" markdown="0">
flowchart LR
    LC[5 x load cells] --> Q[5 x QLST indicators]
    Q -->|RS-485 115200| SH[RS-485 to UART shield]
    SH -->|USART2| MCU[STM32F4]
    MCU --> TIM[TIM6 motion tick]
    MCU --> EXTI[EXTI drum event]
    MCU --> MB[Modbus master]
    TIM --> DRV[Step drivers]
    DRV --> MECH[Drum, nozzles, pumps]
    PI[Raspberry Pi HMI] <-->|USB CDC| MCU
</div>

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

One bottle: nozzle down, soft start, fast fill, slow finish, nozzle up.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/filling/single_fill.mp4" type="video/mp4">
</video>

A longer run of the line.

<video controls playsinline preload="metadata" width="100%" style="max-width:720px; border-radius:6px;">
  <source src="{{ site.url }}{{ site.baseurl }}/videos/filling/fill_cycle.mp4" type="video/mp4">
</video>

---

### Hardware I added

The shield is a protoboard that reuses the stepper board connector. Two pins that used to be HX711 clock and data are remapped to USART2 and fed into a MAX485-class transceiver.

| Shield | QLST indicator | Bottle on the drum |
| :---: | :---: | :---: |
| ![RS-485 to UART shield]({{ site.url }}{{ site.baseurl }}/images/filling/02_shield_top.jpg) | ![QLST weighing indicator]({{ site.url }}{{ site.baseurl }}/images/filling/04_qlst_indicator.jpg) | ![Bottle seated on the drum carrier]({{ site.url }}{{ site.baseurl }}/images/filling/06_field_closeup.jpg) |

Modbus master: non-blocking state machine, 35 ms between polls, 25 ms response timeout, CRC-16, then the next slave. One silent indicator does not stall the other four. Register `0x11` is used for peeling (runtime tare). Register `0x12` rewrites the EEPROM zero and is not touched at runtime.

---

### Field work

Three months on the running line, not only the bench:

- A first attempt kept the old weighing MCU and hijacked one HX711 pin pair as UART. It ran, with jitter and overfill, so that board was removed.
- A fifth column stretched the poll time. Bus speed went to 115200 and the indicators to a 40 Hz update.
- Reused loom connectors corroded. The harness was rebuilt with ferrules, snap connectors, a twisted pair, and the drain bonded to the PG pin on each indicator.
- The nozzle was touching the bottle, and the load cell was grazing the drum plate. Both put false mass on the scale. A firmware pre-arm gate now waits for real weight and for the nozzle to be down.

---

### Tools

**MCU:** STM32F407VG, TIM6, USART2, USB CDC, EXTI  
**Bus:** Modbus RTU, RS-485, QLST indicators  
**Tools:** STM32CubeIDE, STM32CubeMX, HAL  
**Kept from the 2021 machine:** mechanics, stepper carrier board, Darlington array, industrial step drivers

---

### GitHub

[Project on GitHub](https://github.com/alibeknakhimov/gravimetric-filling-line)
