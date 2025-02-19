# robotic_hand_pcb

Robotic hand project for actuators/power electronics and sensors/instrumentation
university courses (PCB hardware).

STM32F446RE firmware
repo: [robotic_hand](https://github.com/danielljeon/robotic_hand).

- System architecture is explained in further detail in this software repo.

---

<details markdown="1">
  <summary>Table of Contents</summary>

<!-- TOC -->
* [robotic_hand_pcb](#robotic_hand_pcb)
  * [1 Overview](#1-overview)
  * [2 Board Specifications](#2-board-specifications)
    * [2.1 Connectors](#21-connectors)
    * [2.2 Switches & Jumpers](#22-switches--jumpers)
  * [3 Release Notes](#3-release-notes)
  * [4 Switching Buck Regulator](#4-switching-buck-regulator)
    * [4.1 Load Current Estimation](#41-load-current-estimation)
    * [4.2 Switching Frequency Selection](#42-switching-frequency-selection)
    * [4.3 Inductor Selection](#43-inductor-selection)
    * [4.4 Output Capacitor Selection](#44-output-capacitor-selection)
    * [4.5 Input Capacitor Selection](#45-input-capacitor-selection)
  * [💖 Sponsors](#-sponsors)
    * [PCBWay](#pcbway)
      * [Why PCBWay?](#why-pcbway)
<!-- TOC -->

</details>

---

<a href="https://www.pcbway.com">
  <img src="docs/PCBWay.svg" alt="PCBWay Logo" style="width:25%;" />
</a>

This project is sponsored by **PCBWay**. You can learn more about their valuable
support here: [💖 Sponsors/PCBWay](#pcbway).

---

## 1 Overview

---

## 2 Board Specifications

### 2.1 Connectors

| Connector          | Ref | Description                 |
|--------------------|:---:|-----------------------------|
| Tag-Connect TC2050 |     | Programming/debug connector |

### 2.2 Switches & Jumpers

| Switch/Jumper     | Ref | Description                           |
|-------------------|:---:|---------------------------------------|
| MCU NRESET switch |     | Generic 6 mm TH button, push to reset |

---

## 3 Release Notes

---

## 4 Switching Buck Regulator

The LMR51430 was chosen for its wide output voltage range of 4.5 V to 36 V, high
current 3 A and efficiency.

> Datasheet: Datasheet: LMR51430 SLUSEF4A – JUNE 2022 – REVISED NOVEMBER 2022.

### 4.1 Load Current Estimation

The expected current load is as follows:

| Load              | Current | n |
|-------------------|---------|---|
| Motors & drivers  | 500 mA  | 5 |
| WS2812B leds      | 60 mA   | 1 |
| 3.3 V ICs via LDO | 100 mA  | 1 |

Approximate total current draw: 2660 mA or 2.66 A at 5 V.

In a real-world engineering scenario, current draw would be estimated using more
accurate models and measured across various revisions of development boards.
However, due to budget constraints and the need to support future higher-voltage
applications, a 3 A buck regulator was chosen, as it is a commonly available and
cost-effective option.

While this provides only an 11% safety margin, it was deemed acceptable for
early research and educational purposes. In the worst-case scenario, the board
can also be powered by an external 5 V power supply if needed.

### 4.2 Switching Frequency Selection

The LMR51430 is capable of 500 kHz and 1.1 MHz. 500 kHz is used reduced noise
and its application of motor current driving. A lower switching frequency would
theoretically reduce switching losses and improve efficiency at high current
draw.

### 4.3 Inductor Selection

The following calculations are based on the recommendations provided in the
LMR51430 datasheet.

The desired peak-to-peak ripple current is given by the following:

$$\Delta i_L = \frac{V_{OUT} \times \left( V_{IN \space MAX} - V_{OUT} \right)}{V_{IN \space MAX} \times L \times f_{SW}}$$

- $L$ = Inductance driving ripple.

The minimum inductor specification is given by:

$$L_{min} = \frac{V_{\text{IN_MAX}} - V_{\text{OUT}}}{I_{\text{OUT}} \times K_{\text{IND}}} \times \frac{V_{\text{OUT}}}{V_{\text{IN_MAX}} \times f_{\text{SW}}}$$
$$L_{min} = \frac{12 \space \text{V} - 5 \space \text{V}}{3 \space \text{A} \times 0.3} \times \frac{5 \space \text{V}}{12 \space \text{V} \times 500 \times 10^{3} \space \text{Hz}}$$
$$L_{min} = 6.84 \times 10^{-6} \space \text{H} = 6.84 \space \mathrm{\mu H}$$

- $K_{IND}$ = Coefficient representing the inductor ripple current as a fraction
  of the maximum output current, utilizing 0.3 for this project.
    - > A reasonable value of KIND must be 20% to 60% of maximum IOUT supported
      > by the converter.
      >
      > - Datasheet section: _9.2.2.4 Inductor Selection_.
- $f_{\text{SW}}$ = Switching frequency, 500 kHz chosen.
- $V_{\text{IN_MAX}}$ = Input voltage, expected 12 V with ±1 V, 13 V.
- $V_{\text{OUT}}$ = Output voltage, 5 V.
- $I_{\text{OUT}}$ = Output current, approximately 3 A.

> During an instantaneous over-current operation event, the RMS and peak
> inductor current can be high. The inductor saturation current must be higher
> than peak current limit level.
>
> In general, choose lower inductance in switching power supplies because it
> usually corresponds to faster transient response, smaller DCR, and reduced
> size for more compact designs. Too low of an inductance can generate too large
> of an inductor current ripple such that over-current protection at the full
> load can be falsely triggered and generates more inductor core loss because
> the current ripple is larger. Larger inductor current ripple also implies
> larger output voltage ripple with the same output capacitors. With peak
> current mode control,ensure there is an adequate amount of inductor ripple
> current. A larger inductor ripple current improves the comparator
> signal-to-noise ratio.
>
> - Datasheet section: _9.2.2.4 Inductor Selection_.

The final minimum inductance value of 6.84 μH closely matches the example
application values provided for a 12V input and 5V output, which uses 6.8 μH.

### 4.4 Output Capacitor Selection

> Minimize the output capacitance to keep cost and size down. The output
> capacitor or capacitors, COUT, must be chosen with care because it directly
> affects the steady state output voltage ripple, loop stability, and output
> voltage overshoot and undershoot during load current transient. The output
> voltage ripple is essentially composed of two parts. One part is caused by the
> inductor ripple current flowing through the Equivalent Series Resistance (ESR)
> of the output capacitors ... The other part is caused by the inductor current
> ripple charging and discharging the output capacitors.
>
> - Datasheet section: _9.2.2.5 Output Capacitor Selection_.

Inductor current ripple flowing through the ESR is given by:

$$\Delta V_{\text{OUT_ESR}} = \Delta i_L \times \text{ESR} = K_{\text{IND}} \times I_{\text{OUT}} \times \text{ESR}$$

Inductor current ripple charning and discharging the output capacitors is given
by:

$$\Delta V_{\text{OUT_C}} = \frac{\Delta i_L}{8 \times f_{\text{SW}} \times C_{\text{OUT}}} = \frac{K_{\text{IND}} \times I_{\text{OUT}}}{8 \times f_{\text{SW}} \times C_{\text{OUT}}}$$

- $K_{\text{IND}}$ = target ripple ratio of the inductor
  current ($\Delta i L / I_{\text{OUT}}$).

The minimum output capacitance needed for a specified output voltage overshoot
and undershoot is given by:

$$C_{\text{OUT}} > \frac{1}{2} \times \frac{8 \times (I_{\text{OH}} - I_{\text{OL}})}{f_{\text{SW}} \times \Delta V_{\text{OUT_SHOOT}}}$$

- $I_{\text{OL}}$ = low level output current during load transient.
- $I_{\text{OH}}$ = high level output current during load transient.
- $V_{\text{OUT_SHOOT}}$ = target output voltage overshoot or undershoot.

For this project a target output ripple is arbitrarily set for 30 mV and the
ripple ratio of inductor current is set to 0.3.

- $\Delta V_{\text{OUT_ESR}} = \Delta V_{\text{OUT_C}} = 30 mV$
- $K_{\text{IND}} = 0.3$

This yields ESR < 75 mΩ and output capacitance > 14 μF.

### 4.5 Input Capacitor Selection

> The LMR51430 device requires a high frequency input decoupling capacitor or
> capacitor. The typical recommended value for the high frequency decoupling
> capacitor is 4.7 μF or higher. TI recommends a high-quality ceramic type X5R
> or X7R with a sufficient voltage rating. The voltage rating must be greater
> than the maximum input voltage. To compensate the derating of ceramic
> capacitors, TI recommends a voltage rating of twice the maximum input voltage.
>
> - Datasheet section: _9.2.2.5 Output Capacitor Selection_.

---

## 💖 Sponsors

### PCBWay

<a href="https://www.pcbway.com">
  <img src="docs/PCBWay.svg" alt="PCBWay Logo" style="width:25%;" />
</a>

This project is sponsored by [**PCBWay**](https://www.pcbway.com), whose PCB
manufacturing services are essential in producing high-quality prototypes for
its development. Their support ensures reliable boards that meet the project's
demands.

#### Why PCBWay?

PCBWay stands out for their exceptional services and commitment to the
community:

- **PCB Manufacturing**: High-quality fabrication with options for multilayer,
  rigid-flex, and advanced designs.
- **PCB Assembly**: Comprehensive solutions, including soldering, component
  sourcing, and assembly.
- **CNC Machining & 3D Printing**: Additional prototyping options to support
  complete product development.
- **Fast Turnaround**: Reliable and quick production times to keep projects on
  schedule.
- **Support for Open Source & Education**: PCBWay actively sponsors projects and
  provides educational resources like tutorials, videos, and documentation,
  empowering developers and hobbyists.
    - This commitment to education and open-source advocacy was a key factor in
      choosing them as a partner 🙂.

Their dedication to professional-grade services and fostering innovation makes
PCBWay an invaluable partner in bringing this project to life.

Learn more here: [Why PCBWay?](https://www.pcbway.com/why.html)
