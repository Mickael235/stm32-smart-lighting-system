# STM32 Smart Lighting System

<p align="center">
  <strong>Embedded sensing and actuator-control system based on STM32</strong><br>
  GPIO · ADC · PWM · UART · STM32 HAL
</p>

<p align="center">
  <img src="assets/images/Test%20Setup%202.png"
       alt="STM32 smart lighting experimental setup"
       width="800">
</p>

## Overview

**STM32 Smart Lighting System** is an embedded-systems project developed at  
**ENSIM – Le Mans University** during the 2025–2026 academic year.

The project explores the design and experimental validation of an automatic lighting-control system built around an **STM32 NUCLEO-L496ZG**.

The intended system activates lighting according to two environmental conditions:

- human presence detected using a **PIR sensor**;
- insufficient ambient light measured using an **LDR**.

The project focuses on sensor acquisition, STM32 peripheral configuration, actuator control, UART diagnostics and incremental hardware/software validation.

---

## Functional principle

The target decision logic is:

```text
LIGHT_ON = PRESENCE AND (LIGHT_LEVEL < THRESHOLD)
```

The purpose is to activate lighting only when someone is present and the measured ambient-light level is insufficient.

---

## Hardware prototype

<p align="center">
  <img src="assets/images/Global%20Assembly.png"
       alt="STM32 smart lighting hardware assembly"
       width="750">
</p>

### Main components

- **STM32 NUCLEO-L496ZG**
- STM32L496ZG — ARM Cortex-M4
- PIR motion / presence sensor
- LDR photoresistor
- FS5103B servo motor
- status LEDs
- breadboard
- passive components

### STM32 peripherals

| Function | STM32 peripheral |
|---|---|
| Presence detection | GPIO |
| Ambient-light acquisition | ADC |
| Servo position control | Timer / PWM |
| Serial monitoring | UART |

---

## STM32 platform

<p align="center">
  <img src="assets/images/STM%20Board%20Assembly.png"
       alt="STM32 NUCLEO-L496ZG assembly"
       width="650">
</p>

The prototype is based on an **STM32 NUCLEO-L496ZG**, integrating an STM32L496ZG ARM Cortex-M4 microcontroller.

The board is responsible for acquiring sensor information, executing the decision logic and controlling the connected actuator.

---

## Pin configuration

| Element | STM32 pin / peripheral | Configuration |
|---|---|---|
| PIR sensor | PF14 | Digital input with pull-down |
| Green LED | PB7 | Digital output |
| LDR voltage divider | PA3 / ADC1_IN8 | 12-bit analog input |
| Red LED | PB14 | Digital output |
| FS5103B servo | TIM3_CH2 | PWM output |
| Serial terminal | LPUART1 | UART transmission |

---

# Incremental validation

Rather than immediately integrating every component into one firmware application, the project followed an **incremental validation strategy**.

Each critical subsystem was implemented and tested independently.

This made it possible to isolate GPIO, ADC, PWM and timing-related issues before attempting complete system integration.

---

## 1. PIR presence detection

The PIR sensor is connected to a digital GPIO input.

<p align="center">
  <img src="assets/images/Presence%20Sensor.png"
       alt="PIR presence sensor used in the STM32 project"
       width="550">
</p>

The firmware detects changes in the sensor state and provides visual and serial feedback.

### Validated functions

- digital GPIO acquisition;
- input pull-down configuration;
- presence / absence detection;
- LED status indication;
- state-change detection;
- UART diagnostics.

Example serial output:

```text
Presence detected
Absence detected
```

### UART validation

<p align="center">
  <img src="assets/images/Teraterm-%20Prsence%20Sensor%20Test.png"
       alt="PIR presence sensor UART validation using TeraTerm"
       width="750">
</p>

TeraTerm was used to verify the events transmitted by the STM32 through UART during presence-detection tests.

---

## 2. Ambient-light measurement

Ambient luminosity is measured using an **LDR voltage divider** connected to the STM32 ADC.

<p align="center">
  <img src="assets/images/Light%20Sensor.png"
       alt="LDR light sensor circuit"
       width="550">
</p>

The analog voltage is converted by **ADC1** using a 12-bit conversion.

The resulting value is interpreted in the range:

```text
0 ? 4095
```

A configurable threshold separates low-light and sufficient-light conditions.

Example logic:

```c
if (adc_value < LIGHT_THRESHOLD)
{
    // Low-light condition
}
```

### Validated functions

- ADC1 configuration;
- analog acquisition;
- 12-bit conversion;
- luminosity-level interpretation;
- threshold comparison;
- LED feedback;
- UART monitoring.

### ADC / UART validation

<p align="center">
  <img src="assets/images/Teraterm-%20Light%20Sensor%20Test.png"
       alt="LDR ADC measurements displayed through TeraTerm"
       width="750">
</p>

The UART output makes it possible to monitor the ADC values during changes in ambient illumination and verify the selected threshold experimentally.

---

## 3. Servo motor control

The project also validates actuator control using an **FS5103B servo motor**.

<p align="center">
  <img src="assets/images/Servo%20Motor.png"
       alt="Servo motor used in the STM32 project"
       width="550">
</p>

The servo is driven using a hardware PWM signal generated by **Timer 3**.

Target PWM characteristics:

```text
Frequency: ~50 Hz
Period:    ~20 ms
Pulse:     ~1–2 ms
```

The position is controlled by modifying the timer comparison register.

### Validated functions

- Timer 3 configuration;
- PWM generation;
- comparison-register control;
- stable servo movement;
- repeatable positioning;
- mechanical response to different commands.

---

# Software architecture

The firmware was developed using **STM32CubeIDE**, STM32CubeMX configuration and the STM32 HAL.

A typical application follows this execution structure:

```text
Initialization
¦
+-- HAL_Init()
+-- SystemClock_Config()
+-- GPIO configuration
+-- ADC configuration
+-- Timer / PWM configuration
+-- UART configuration
        ¦
        ?
     while(1)
        ¦
        +-- Sensor acquisition
        +-- Decision logic
        +-- Actuator control
        +-- Serial diagnostics
```

---

# Experimental setup

<p align="center">
  <img src="assets/images/Test%20Setup%201.png"
       alt="STM32 smart lighting test setup"
       width="750">
</p>

The subsystems were tested directly on the physical STM32 prototype using breadboard wiring and serial diagnostics.

The validation campaign focused on observing the behaviour of each peripheral independently before combining them.

---

# Experimental results

## PIR / GPIO

Validated:

- stable digital acquisition;
- correct presence-state detection;
- event detection on state changes;
- coherent UART messages;
- correct visual LED feedback.

## LDR / ADC

Validated:

- successful analog acquisition;
- distinction between bright and dark conditions;
- experimentally observable ADC variations;
- threshold-based decision logic;
- UART monitoring.

## Servo / PWM

Validated:

- stable PWM generation;
- successful servo positioning;
- repeatable response to comparison-register changes.

---

# Integration findings

The individual hardware and firmware subsystems were successfully validated.

During the attempt to combine all functions into a single STM32 application, several system-level integration issues were identified:

- blocking `HAL_Delay()` calls;
- conflicts between peripheral configurations;
- synchronization between ADC acquisition and PIR processing;
- interactions between timing-sensitive operations.

For this reason, the repository preserves **three independently validated STM32 applications** rather than presenting the complete system as fully integrated.

This reflects the actual engineering state of the project:

```text
PIR / GPIO        ? validated
LDR / ADC         ? validated
Servo / PWM       ? validated

Complete firmware integration
                   ? further work required
```

---

# Repository structure

```text
stm32-smart-lighting-system/
¦
+-- assets/
¦   +-- images/
¦       +-- Global Assembly.png
¦       +-- Light Sensor.png
¦       +-- Presence Sensor.png
¦       +-- STM Board Assembly.png
¦       +-- Servo Motor.png
¦       +-- Teraterm- Light Sensor Test.png
¦       +-- Teraterm- Prsence Sensor Test.png
¦       +-- Test Setup 1.png
¦       +-- Test Setup 2.png
¦
+-- firmware/
¦   +-- pir-presence/
¦   +-- ldr-luminosity/
¦   +-- servo-pwm/
¦
+-- docs/
¦   +-- report.pdf
¦
+-- .gitignore
+-- README.md
```

Each directory under `firmware/` contains an independent STM32CubeIDE project used to validate one critical subsystem.

---

# Development environment

### Embedded development

- STM32CubeIDE
- STM32CubeMX
- STM32 HAL
- ARM GCC
- ST-LINK

### Debugging and validation

- TeraTerm
- UART diagnostics
- breadboard prototyping
- hardware testing

### Version control

- Git
- GitHub

---

# Technologies

`STM32` · `C` · `ARM Cortex-M4` · `STM32 HAL` · `GPIO` · `ADC` · `PWM` · `UART` · `Sensors` · `Embedded Systems`

---

# Possible improvements

A future version could integrate all three subsystems using a more robust real-time software architecture.

Possible improvements include:

- non-blocking state machine;
- interrupt-based sensor processing;
- SysTick-based scheduling;
- ADC moving-average filtering;
- luminosity-threshold hysteresis;
- PIR debounce / validation timing;
- configurable lighting hold time;
- complete integration of sensing and actuator control.

A possible future architecture could be:

```text
               +--------------+
               ¦  PIR sensor  ¦
               +--------------+
                      ¦ GPIO
                      ?
+------------+   +----------+
¦ LDR sensor ¦--?¦  STM32   ¦
+------------+ADC¦ L496ZG   ¦
                 +----------+
                      ¦
             Decision / State Machine
                      ¦
             +-----------------+
             ?                 ?
          Lighting           Servo
                            PWM / TIM3
```

---

# Documentation

The complete academic report is available here:

### [?? Project report](docs/report.pdf)

It includes:

- hardware architecture;
- STM32 pin configuration;
- PIR validation;
- LDR / ADC acquisition;
- PWM servo control;
- experimental results;
- integration analysis;
- identified limitations;
- possible improvements.

---

# Authors

**Michael Essomba**  
**Arsène Nguenang Youkap**

ENSIM – Le Mans University  
Academic year: **2025–2026**