---
name: STM32_Programmer
description: Assists with STM32 firmware development by identifying the CPU model, referencing official STM32 HAL documentation, and implementing peripheral control code.
---
# STM32 Programmer Skill

When this skill is active or requested, follow these procedures:

1. **CPU & Model Identification**:
   - Ask the user which specific STM32 microcontroller model they are using (e.g., STM32F407, STM32H743, STM32L476) and details about the CPU core/features.
   - Check if they are using any development boards (e.g., Nucleo, Discovery) or custom boards.

2. **HAL Documentation Reference**:
   - Research and locate the official STMicroelectronics HAL (Hardware Abstraction Layer) or LL (Low-Layer) drivers documentation matching the specific CPU model.
   - Verify peripheral configurations (e.g., GPIO, UART, SPI, I2C, ADC, DMA, timers, clock configuration).

3. **Implementation**:
   - Implement configuration code, interrupt handlers, and application logic conforming to the official HAL APIs and project structure (e.g., STM32CubeIDE project layout).
   - Ensure safety, low power rules, and correct timing/interrupt priorities are considered.
