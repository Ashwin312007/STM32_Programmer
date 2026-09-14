# STM32 Programmer Skill

[![Skill Spec](https://img.shields.io/badge/AI--Skill-v2.0-blue.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![STM32 HAL / LL](https://img.shields.io/badge/STM32-HAL%20%2F%20LL-red.svg)](#)

An AI agent skill for **reliable STM32 firmware engineering**. It helps coding agents inspect an existing STM32Cube project, identify the exact MCU and board, preserve generated-code boundaries, verify clocks and peripheral configuration, implement HAL/LL firmware safely, debug systematically, and validate behavior with build and hardware evidence.

---

## What it covers

- **Target discovery** — exact STM32 part, board, core, toolchain, Cube package, debugger, and RTOS.
- **STM32Cube awareness** — `.ioc` inspection, generated-code boundaries, `USER CODE` preservation, and CubeMX-compatible edits.
- **Official documentation workflow** — datasheet, reference manual, errata, HAL/LL docs, board manuals, and schematics.
- **Clock/RCC validation** — SYSCLK, AHB/APB, peripheral kernel clocks, PLLs, flash latency, and timer clock calculations.
- **Peripheral engineering** — GPIO, EXTI, timers, PWM, encoders, input capture, ADC, DAC, DMA, UART, I2C, SPI, CAN/FDCAN, USB, Ethernet, SDMMC, and memory.
- **Interrupts and RTOS** — NVIC paths, ISR rules, FreeRTOS ownership, ISR-safe synchronization, and priority checks.
- **Memory/startup** — linker scripts, vector tables, bootloader offsets, stack/heap, special RAM regions, and DMA accessibility.
- **Debugging** — a fixed hardware-to-software diagnostic order instead of random rewrites.
- **Verification** — build/link checks plus runtime measurements using SWD, UART, logic analyzers, oscilloscopes, and other available evidence.
- **Hardware safety** — safe output states, electrical limits, driver-stage checks, and no automatic actuator enabling.

---

## Engineering workflow

```text
Identify exact target
        ↓
Inspect project + .ioc
        ↓
Verify official device documentation
        ↓
Classify active peripheral/task
        ↓
Verify clocks, pins, IRQ/DMA paths
        ↓
Make minimal implementation changes
        ↓
Build + link verification
        ↓
Runtime / hardware verification
        ↓
Report evidence and remaining checks
```

The skill deliberately avoids assumptions such as using pin mappings from a similar STM32, calculating timing from an assumed CPU clock, or declaring a hardware fix successful because the code compiled.

---

## Example task categories

```text
TARGET            CUBEMX            CLOCK_RCC
GPIO              EXTI              TIMER
PWM               ENCODER           INPUT_CAPTURE
ADC               DAC               DMA
UART_USART        I2C               SPI
CAN_FDCAN         USB               ETHERNET
SDMMC             MEMORY            INTERRUPT_NVIC
FREERTOS          LOW_POWER         BOOT_STARTUP
LINKER            DEBUGGING         OPTIMIZATION
```

---

## Repository structure

```text
STM32_Programmer/
├── SKILL.md       # STM32-specific engineering rules and workflow
├── README.md      # Overview and usage
└── LICENSE        # MIT License
```

---

## Usage

Place the `STM32_Programmer` directory in the skills directory used by your AI coding agent, then activate it for STM32 firmware work.

Example:

```bash
~/.gemini/config/skills/STM32_Programmer/
```

It is designed to work well beside a general workflow skill such as `Essential_Skill`: the general skill controls planning/execution policy, while `STM32_Programmer` supplies the embedded-domain rules.

---

## Core principle

**Inspect the real target, verify the hardware path, then change the minimum code required.**

Compilation is evidence that the firmware builds. It is not evidence that a PWM frequency, ADC reading, DMA transfer, communication bus, interrupt, or actuator behaves correctly on hardware.

---

## License

Distributed under the MIT License. See `LICENSE` for details.
