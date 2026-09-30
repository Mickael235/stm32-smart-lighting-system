# STM32 Smart Lighting System

Embedded systems academic project developed at ENSIM – Le Mans University.

The goal of the project is to design an automatic lighting system using an
STM32 NUCLEO-L496ZG board.

The lighting decision is based on two conditions:

- presence detected by a PIR sensor;
- insufficient ambient light measured using an LDR.

## Hardware

- STM32 NUCLEO-L496ZG
- STM32L496ZG – ARM Cortex-M4
- PIR motion sensor
- LDR light sensor
- FS5103B servo motor
- LEDs
- Breadboard

## Embedded peripherals

- GPIO
- ADC
- PWM / Timer
- UART

## Development environment

- STM32CubeIDE
- STM32CubeMX
- STM32 HAL
- ARM GCC
- ST-LINK
- TeraTerm

## Repository structure

```text
firmware/
├── pir-presence/
├── ldr-luminosity/
└── servo-pwm/

docs/
└── report.pdf

Validation strategy
The project was developed using an incremental validation approach.
Each critical function was first tested independently:
1. PIR presence detection
2. LDR luminosity acquisition using ADC
3. Servo control using PWM
This approach made it possible to isolate hardware and software configuration
issues before attempting full system integration.
Functional logic
LIGHT_ON = PRESENCE AND (LIGHT_LEVEL < THRESHOLD)

Results
- Stable PIR detection
- ADC-based luminosity measurement
- UART monitoring
- Stable PWM servo control
- Independent validation of each subsystem
Limitations and future improvements
The complete integration of all subsystems revealed synchronization and
blocking-delay issues.
Possible improvements include:
- non-blocking state machine;
- interrupt-based execution;
- SysTick scheduling;
- ADC filtering;
- threshold hysteresis;
- improved sensor robustness.
Authors
- Michael Essomba
- Arsène Nguenang Youkap
Academic year: 2025–2026