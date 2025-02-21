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
  * [4 Development Process](#4-development-process)
    * [4.1 Simulations: PSpice for TI](#41-simulations-pspice-for-ti)
    * [4.2 PCB Design: KiCad](#42-pcb-design-kicad)
    * [4.3 Firmware: CLion and STM32CubeMX](#43-firmware-clion-and-stm32cubemx)
    * [4.4. Version Control: GitHub](#44-version-control-github)
  * [5 Switching Buck Regulator](#5-switching-buck-regulator)
    * [5.1 Load Current Estimation](#51-load-current-estimation)
    * [5.2 Switching Frequency Selection](#52-switching-frequency-selection)
    * [5.3 Inductor Selection](#53-inductor-selection)
    * [5.4 Output Capacitor Selection](#54-output-capacitor-selection)
    * [5.5 Input Capacitor Selection](#55-input-capacitor-selection)
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

| Connector                       | Ref | Description                                           |
|---------------------------------|:---:|-------------------------------------------------------|
| Tag-Connect TC2050              |     | Programming/debug connector                           |
| UART Breakout                   |     | General purpose UART breakout                         |
| VL53L4CD Breakout               |     | Breakout connector for TOF sensor                     |
| WS2812B PWM LEDs                |     | 5 V Addressable LED breakout                          |
| Battery to buck (5 V) regulator |     | Pin 1: Ground, Pin 2: Battery supply (> 5 V, <= 36 V) |
| Unipolar Motor 1                |     | Robotic finger 1 motor                                |
| Unipolar Motor 2                |     | Robotic finger 2 motor                                |
| Unipolar Motor 3                |     | Robotic finger 3 motor                                |
| Unipolar Motor 4                |     | Robotic finger 4 motor                                |
| Unipolar Motor 5                |     | Robotic finger 5 motor                                |
| ADC 1                           |     | 5 V analog input 1                                    |
| ADC 2                           |     | 5 V analog input 2                                    |
| ADC 3                           |     | 5 V analog input 3                                    |
| ADC 4                           |     | 5 V analog input 4                                    |
| ADC 5                           |     | 5 V analog input 5                                    |
| ADC 6                           |     | 5 V analog input 6                                    |
| ADC 7                           |     | 5 V analog input 7                                    |

### 2.2 Switches & Jumpers

| Switch/Jumper         | Ref | Description                                           |
|-----------------------|:---:|-------------------------------------------------------|
| MCU NRESET switch     |     | Generic 6 mm TH button, push to reset                 |
| BNO085 clock select   |     | Open = crystal, closed = external/internal            |
| VL53L4CD breakout INT |     | Open = no breakout, closed = bridge breakout INT line |

---

## 3 Release Notes

---

## 4 Development Process

### 4.1 Simulations: PSpice for TI

[PSpice for TI](https://www.ti.com/tool/PSPICE-FOR-TI) by Cadence and Texas
Instruments (TI) is a free limited-feature version of Cadence PSpice,
specifically made for Texas Instruments' analog and power electronics
components.

Given the significant use of TI components (ADC IC, darlington transistor arrays
and buck regulators) for this project, PSpice for TI was chosen as the primarily
design verification and virtual testing tool.

### 4.2 PCB Design: KiCad

[KiCad](https://www.kicad.org/) was chosen for PCB design due to its ease of use
as a free, open-source software. Additionally, prior experience and personal
preference played a key role in the decision.

### 4.3 Firmware: CLion and STM32CubeMX

[CLion](https://www.jetbrains.com/clion/)
and [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html) from
STMicroelectronics were selected based on prior experience and personal
preference. STM32CubeMX is utilized for initial component selection, pin
configuration, and HAL software generation, while CLion is used for general C
firmware development. For more details,
see [robotic_hand](https://github.com/danielljeon/robotic_hand).

### 4.4. Version Control: GitHub

[GitHub](https://github.com/) was chosen for its efficiency in version control
and support for Actions automations. These automations facilitated testing,
validation, and various manufacturing-related processes for both hardware and
software development.

---

## 5 Switching Buck Regulator

The LMR51430 was chosen for its wide output voltage range of 4.5 V to 36 V, high
current 3 A and efficiency.

> Datasheet: Datasheet: LMR51430 SLUSEF4A – JUNE 2022 – REVISED NOVEMBER 2022.

> Big thanks, as always, to the amazing publicly available resources online 👑:
> 1. [Switching Regulator PCB Design - Phil's Lab #60](https://www.youtube.com/watch?v=AmfLhT5SntE)
> 2. [Switching Regulator Component Selection & Sizing - Phil's Lab #71](https://www.youtube.com/watch?v=FqT_Ofd54fo)

### 5.1 Load Current Estimation

The expected current load is as follows:

| Load              | Current | n |
|-------------------|---------|---|
| Motors + drivers  | 500 mA  | 5 |
| WS2812B leds      | 60 mA   | 1 |
| 3.3 V ICs via LDO | 100 mA  | 1 |

Approximate total current draw: 2660 mA = 2.66 A at 5 V.

In a real-world engineering scenario, current draw would be estimated using more
accurate models and measured across various revisions of development boards.
However, due to budget constraints and the need to support future higher-voltage
applications, a 3 A buck regulator was chosen, as it is a commonly available and
cost-effective option.

While this provides only an 11% safety margin, it was deemed acceptable for
early research and educational purposes. In the worst-case scenario, the board
can also be powered by an external 5 V power supply if needed.

### 5.2 Switching Frequency Selection

The LMR51430 is capable of 500 kHz and 1.1 MHz. 500 kHz is used reduced noise
and its application of motor current driving. A lower switching frequency would
theoretically reduce switching losses and improve efficiency at high current
draw.

### 5.3 Inductor Selection

The following calculations are based on the recommendations provided in the
LMR51430 datasheet.

The desired peak-to-peak ripple current is given by the following:

$$\Delta i_L = \frac{V_{OUT} \times \left( V_{IN \space MAX} - V_{OUT} \right)}{V_{IN \space MAX} \times L \times f_{SW}}$$

- $L$ = Inductance driving ripple.

The minimum inductor specification is given by:

$$L_{min} = \frac{V_{\text{IN MAX}} - V_{\text{OUT}}}{I_{\text{OUT}} \times K_{\text{IND}}} \times \frac{V_{\text{OUT}}}{V_{\text{IN MAX}} \times f_{\text{SW}}}$$
$$L_{min} = \frac{12 \space \text{V} - 5 \space \text{V}}{3 \space \text{A} \times 0.3} \times \frac{5 \space \text{V}}{12 \space \text{V} \times 500 \times 10^{3} \space \text{Hz}}$$
$$L_{min} = 6.84 \times 10^{-6} \space \text{H} = 6.84 \space \mathrm{\mu H}$$

- $K_{IND}$ = Coefficient representing the inductor ripple current as a fraction
  of the maximum output current, utilizing 0.3 for this project.
    - > A reasonable value of KIND must be 20% to 60% of maximum IOUT supported
      > by the converter.
      >
      > - Datasheet section: _9.2.2.4 Inductor Selection_.
- $f_{\text{SW}}$ = Switching frequency, 500 kHz chosen.
- $V_{\text{IN MAX}}$ = Input voltage, expected 12 V with ±1 V, 13 V.
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

### 5.4 Output Capacitor Selection

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

$$\Delta V_{\text{OUT ESR}} = \Delta i_L \times \text{ESR} = K_{\text{IND}} \times I_{\text{OUT}} \times \text{ESR}$$

Inductor current ripple charging and discharging the output capacitors is given
by:

$$\Delta V_{\text{OUT C}} = \frac{\Delta i_L}{8 \times f_{\text{SW}} \times C_{\text{OUT}}} = \frac{K_{\text{IND}} \times I_{\text{OUT}}}{8 \times f_{\text{SW}} \times C_{\text{OUT}}}$$

- $K_{\text{IND}}$ = target ripple ratio of the inductor
  current ($\Delta i L / I_{\text{OUT}}$).

The minimum output capacitance needed for a specified output voltage overshoot
and undershoot is given by:

$$C_{\text{OUT}} > \frac{1}{2} \times \frac{8 \times (I_{\text{OH}} - I_{\text{OL}})}{f_{\text{SW}} \times \Delta V_{\text{OUT SHOOT}}}$$

- $I_{\text{OL}}$ = low level output current during load transient.
- $I_{\text{OH}}$ = high level output current during load transient.
- $V_{\text{OUT SHOOT}}$ = target output voltage overshoot or undershoot.

For this project a target output ripple is arbitrarily set for 30 mV and the
ripple ratio of inductor current is set to 0.3.

- $\Delta V_{\text{OUT ESR}} = \Delta V_{\text{OUT C}} = 30 mV$
- $K_{\text{IND}} = 0.3$

This yields ESR < 75 mΩ and output capacitance > 14 μF.

### 5.5 Input Capacitor Selection

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
