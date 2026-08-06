# STM32 Programmer Skill

[![Skill Spec](https://img.shields.io/badge/AI--Skill-v1.0-blue.svg)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![STM32 HAL / LL](https://img.shields.io/badge/STM32-HAL%20%2F%20LL-red.svg)](#)

An AI agent skill for **STM32 microcontroller firmware development**. It guides AI coding assistants through CPU model identification (e.g. STM32F4, STM32H7, STM32L4), official ST HAL/LL driver documentation lookup, peripheral configuration (GPIO, UART, SPI, I2C, ADC, DMA, Timers), and STM32CubeIDE project integration.

---

## 📋 Features

- **CPU & Board Identification**: Pinpoints specific STM32 microcontrollers (e.g., STM32F407, STM32H743, STM32L476) and dev board targets (Nucleo, Discovery, custom PCBs).
- **HAL & LL Driver Documentation**: Researches STMicroelectronics official Hardware Abstraction Layer (HAL) and Low-Layer (LL) driver specifications matching the CPU core.
- **Peripheral Control & Configuration**: Verifies configuration parameters for GPIO, UART, SPI, I2C, ADC, DMA, timers, and clock trees (RCC).
- **Firmware Implementation**: Generates C/C++ firmware, interrupt service routines (ISRs), low-power configurations, and timing priorities conforming to STM32CubeIDE project layouts.

---

## 📁 Repository Structure

```
STM32_Programmer/
├── SKILL.md       # AI agent skill specification & firmware development workflow
├── README.md      # Documentation and overview
└── LICENSE        # MIT License
```

---

## 🚀 Usage & Integration

### With AI Agents (Gemini, Claude, Antigravity CLI)
Place the `STM32_Programmer` directory into your agent's skills configuration folder:
```bash
~/.gemini/config/skills/STM32_Programmer/
```

When building STM32 firmware or configuring ARM Cortex-M peripherals, the AI agent will load `SKILL.md` to ensure proper ST HAL/LL API usage and CPU hardware safety.

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for details.
