---
name: STM32_Programmer
description: STM32 firmware engineering skill for target discovery, CubeMX/CubeIDE projects, HAL/LL/CMSIS development, clocks, GPIO, timers, ADC, DMA, communication buses, interrupts, FreeRTOS, low power, debugging, hardware safety, and empirical verification.
---

# STM32 Programmer Skill

STM32_Programmer provides domain-specific engineering rules for STM32 firmware work.

If a general workflow skill such as `Essential_Skill` is active, that skill controls planning, approval, execution orchestration, and Git operations. `STM32_Programmer` controls STM32-specific analysis, implementation, debugging, and verification.

---

## 1. Core Rules

### Rule 1 — Identify the Exact Target

Never assume the MCU from a family name alone.

Determine, from the project or user input:

- Exact STM32 part number when available
- Development board or custom PCB
- CPU core and relevant architectural features
- STM32Cube MCU package / HAL version when available
- Toolchain and IDE
- Debug probe
- RTOS or bare-metal environment

Prefer inspecting the existing project before asking the user for information already present in files.

Useful sources include:

- `.ioc`
- project metadata
- linker script
- startup file
- `main.h`
- `stm32xxxx_hal_conf.h`
- board schematic / pinout

Do not use pin mappings, peripheral instances, memory sizes, or clock limits from a similar STM32 as if they apply to the exact target.

---

### Rule 2 — Inspect Before Modification

Before changing firmware, inspect the files relevant to the task.

Typical STM32Cube projects may contain:

```text
Core/Inc/
Core/Src/
Drivers/CMSIS/
Drivers/STM32xxxx_HAL_Driver/
Middlewares/
USB_Device/
FATFS/
LWIP/
*.ioc
*.ld
startup_stm32*.s
```

Inspect, where relevant:

- `main.c`
- `main.h`
- `stm32xxxx_it.c/.h`
- `stm32xxxx_hal_msp.c`
- `system_stm32xxxx.c`
- peripheral source files
- RTOS configuration
- linker script
- startup file

Do not modify unrelated peripherals or files.

---

### Rule 3 — Preserve STM32Cube Generated Code

Treat the `.ioc` file and STM32Cube-generated code as project state.

When code regeneration is expected:

- Prefer changing peripheral configuration in CubeMX / `.ioc` rather than fighting generated initialization code.
- Put application code only in valid `USER CODE BEGIN` / `USER CODE END` regions or separate user-owned files.
- Do not move or rename generated user-code markers.
- Do not place important code in generated regions that CubeMX may overwrite.

If a direct edit to generated code is necessary, explicitly warn that regeneration may replace it.

---

### Rule 4 — Use the Correct Documentation

Use authoritative STM32 documentation matched to the exact device and board.

Prefer this order:

1. Device datasheet
2. Reference manual
3. Device errata sheet
4. STM32Cube HAL / LL driver documentation
5. Board user manual and schematic
6. ARM Cortex-M documentation when the issue is core-specific

Do not infer register fields, alternate functions, DMA mappings, electrical limits, or peripheral behavior when they can be verified from official documentation.

---

### Rule 5 — Preserve the Existing Driver Model

Follow the project's current abstraction unless the task requires a change.

- HAL project -> prefer HAL
- LL project -> prefer LL
- Mixed HAL/LL project -> preserve the existing split
- Register-level code -> use only when already used, explicitly requested, or justified by a measurable requirement

Do not rewrite working HAL code into LL or register-level code merely for style or theoretical performance.

---

### Rule 6 — Hardware Safety First

Before enabling physical outputs, reason about:

- GPIO voltage compatibility
- Output polarity
- Pull-up / pull-down requirements
- Open-drain requirements
- Motor / relay / MOSFET driver stages
- PWM duty-cycle limits
- Current limits
- Power sequencing
- External clock assumptions
- Watchdog behavior
- Fail-safe states

Never assume an STM32 GPIO can directly drive a load that requires a power stage.

Do not automatically enable motors, heaters, high-current loads, or other hazardous actuators during validation.

---

## 2. Task Classification

Classify the task into one or more categories:

```text
TARGET
CUBEMX
CLOCK_RCC
GPIO
EXTI
TIMER
PWM
ENCODER
INPUT_CAPTURE
ADC
DAC
DMA
UART_USART
I2C
SPI
CAN_FDCAN
USB
ETHERNET
SDMMC
MEMORY
INTERRUPT_NVIC
FREERTOS
LOW_POWER
BOOT_STARTUP
LINKER
DEBUGGING
OPTIMIZATION
```

Focus inspection and documentation lookup on the active categories.

---

## 3. Target and Project Discovery

Before implementation, determine:

```text
MCU:
Board:
Clock source:
System clock:
Toolchain:
HAL/LL:
Cube package version:
RTOS:
Debugger:
Relevant peripherals:
```

For an existing project, inspect configuration before proposing new initialization code.

Do not create duplicate peripheral handles or duplicate initialization paths if CubeMX already generated them.

---

## 4. Clock and RCC Work

Clock errors can invalidate every timing-dependent peripheral.

Before changing timers, UART baud rate, ADC timing, USB, or other clock-sensitive features, verify:

- HSI / HSE / MSI / LSE / LSI source selection as applicable
- PLL source and multipliers/dividers
- SYSCLK
- AHB clock
- APB clocks
- Peripheral clock source
- Flash latency
- Voltage scaling where applicable
- Timer clock multiplication behavior on the target family

Do not calculate peripheral timing from an assumed CPU frequency.

When timing matters, show the calculation and the actual clock feeding the peripheral.

---

## 5. GPIO and EXTI

For GPIO changes verify:

- Correct port and pin
- Alternate-function mapping
- Input/output/alternate/analog mode
- Push-pull vs open-drain
- Pull-up / pull-down
- Output speed where relevant
- Initial output level before enabling the pin

For EXTI verify:

- GPIO-to-EXTI mapping
- Trigger edge
- NVIC enable
- Interrupt priority
- Callback / ISR path
- Debounce strategy if the input is mechanical

Do not perform long blocking work inside an interrupt callback.

---

## 6. Timers, PWM, Encoder and Input Capture

For timer work identify the real timer input clock first.

Verify:

- Prescaler
- Auto-reload / period
- Counter mode
- Timer resolution
- PWM frequency
- Duty-cycle range
- Channel
- GPIO alternate function
- Advanced-timer output enable requirements where applicable

Use:

```text
counter_clock = timer_clock / (PSC + 1)
update_frequency = counter_clock / (ARR + 1)
```

Adjust for center-aligned modes or other timer-specific behavior when applicable.

For encoder mode verify channel polarity, filtering, timer width, wraparound handling, and counts-per-revolution interpretation.

For input capture verify edge selection, timer overflow handling, and unit conversion.

---

## 7. ADC and DAC

For ADC work verify:

- Resolution
- Reference voltage assumption
- Input channel
- Sampling time
- ADC clock
- Conversion mode
- Trigger source
- Calibration requirement
- DMA mode if used
- Source impedance and acquisition-time implications when relevant

Do not convert ADC counts to physical units without stating the reference and scaling assumptions.

For DAC work verify output buffer configuration, trigger source, sample rate, and downstream electrical loading.

---

## 8. DMA

When DMA is involved, inspect the full data path:

```text
Peripheral -> DMA request -> DMA controller/channel/stream -> memory buffer -> callback/consumer
```

Verify:

- Correct DMA request mapping
- Direction
- Data width
- Memory increment
- Circular vs normal mode
- Priority
- Buffer length
- Interrupt enable
- Callback ownership

For STM32 devices with data cache, verify cache coherency requirements for DMA buffers before declaring a DMA fault fixed.

Do not place DMA buffers in inaccessible memory regions for the selected DMA engine.

---

## 9. UART, I2C, SPI and CAN/FDCAN

### UART / USART

Verify:

- Peripheral instance
- TX/RX pins and alternate functions
- Baud rate
- Word length
- Parity
- Stop bits
- Oversampling when relevant
- Blocking vs interrupt vs DMA mode

For continuous receive streams, prefer an explicit buffering strategy rather than assuming one blocking receive call is sufficient.

### I2C

Verify:

- 7-bit vs 10-bit addressing
- Device address representation expected by the HAL call
- Pull-up resistors
- Bus speed
- Timing configuration
- Repeated-start requirements
- Timeout and error recovery

Do not confuse a 7-bit slave address with an already-shifted HAL address.

### SPI

Verify:

- Master/slave mode
- Clock polarity
- Clock phase
- Frame size
- Bit order
- Baud prescaler
- Chip-select behavior
- Full/half duplex mode

Use the target device's timing requirements rather than guessing SPI mode.

### CAN / FDCAN

Verify:

- Peripheral type supported by the exact MCU
- Kernel clock
- Nominal bit timing
- Data-phase timing for CAN FD
- Sample point
- Filters
- FIFO selection
- Transceiver presence
- Termination

Do not treat the MCU peripheral as a substitute for a physical CAN transceiver.

---

## 10. Interrupts and NVIC

For interrupt-driven code inspect:

```text
Peripheral interrupt source
-> NVIC configuration
-> IRQHandler
-> HAL/LL IRQ handler
-> callback / application code
```

Verify:

- IRQ is enabled
- Correct handler name is linked
- Peripheral interrupt flag is enabled
- Priority is compatible with the system
- Shared data is synchronized correctly

Keep ISRs short.

Avoid:

- long delays
- blocking peripheral transactions
- heavy logging
- large calculations

inside interrupts unless explicitly justified.

---

## 11. FreeRTOS

When FreeRTOS or CMSIS-RTOS is present, determine:

- API layer in use
- Task ownership of each peripheral
- Interrupt priorities
- Queue/semaphore/mutex usage
- Stack sizes
- Heap scheme
- Tick rate

Do not call RTOS APIs from an ISR unless that API is ISR-safe for the active RTOS layer.

Do not use a mutex from an ISR.

Prefer queues, task notifications, semaphores, or event mechanisms for ISR-to-task handoff.

---

## 12. Memory, Startup and Linker Work

Before changing memory layout inspect:

- Flash origin and size
- RAM regions
- Stack
- Heap
- vector table placement
- bootloader offset if any
- special memory regions such as CCM, DTCM, AXI SRAM, backup SRAM, or external memory where applicable

Do not place data in a special memory region without checking whether the CPU, DMA engine, and peripheral can access it as required.

For bootloader/application projects verify vector-table relocation and linker offsets together.

---

## 13. Low Power

Before adding sleep, stop, or standby behavior verify:

- Wake-up source
- wake-up polarity
- clocks lost or retained
- peripheral state after wake
- GPIO state
- RTC / LSE requirements
- reinitialization required after wake

Do not optimize power by disabling clocks that active peripherals still require.

---

## 14. Implementation Rules

Prefer simple, testable firmware.

- Keep hardware initialization separate from application behavior where the project already supports that structure.
- Prefer non-blocking interrupt/DMA designs when continuous data flow or real-time behavior requires them.
- Blocking HAL calls are acceptable when timing requirements allow them.
- Use explicit timeouts for communication where appropriate.
- Avoid `HAL_Delay()` inside timing-critical control paths.
- Avoid busy-wait loops when an interrupt, DMA, or scheduler mechanism is already appropriate.
- Preserve existing naming and project conventions.
- Do not add abstraction layers that the task does not need.

For shared ISR/main or ISR/task state, consider atomicity and synchronization; adding `volatile` alone does not make multi-step access thread-safe.

---

## 15. Debugging Order

When firmware fails, debug in this order unless evidence points elsewhere:

```text
1. Exact target / board
2. Power, reset and boot configuration
3. Build and link
4. Clock tree
5. Pin mapping / alternate function
6. Peripheral initialization
7. NVIC / interrupt path
8. DMA path
9. Protocol configuration
10. Buffer and memory ownership
11. RTOS scheduling / synchronization
12. Application algorithm
13. Electrical signal integrity / external hardware
```

Do not rewrite the application algorithm before confirming the hardware and peripheral path.

---

## 16. Debug Tools and Evidence

Use the available evidence instead of guessing.

Useful tools include:

- STM32CubeIDE debugger
- ST-LINK / SWD
- breakpoints and watch expressions
- peripheral register view
- fault status registers
- UART logging
- SWV / ITM where supported
- logic analyzer
- oscilloscope
- multimeter

For HardFault or exception debugging inspect the fault context and Cortex-M fault status registers before proposing random code changes.

---

## 17. Build Verification

A successful edit is not a verified firmware change.

Use the project's actual build system and verify:

- clean compile for affected sources
- no new warnings relevant to the change
- successful link
- expected Flash/RAM usage where relevant

Do not claim a hardware behavior is fixed from compilation alone.

---

## 18. Runtime Verification

Match verification to the task.

Examples:

```text
GPIO          -> measure/toggle expected pin
PWM           -> measure frequency and duty cycle
UART          -> verify transmitted and received bytes
I2C/SPI       -> inspect bus transactions
ADC           -> compare raw counts and known input voltage
DMA           -> verify buffer updates and callbacks
Timer         -> measure timing against expected calculation
Encoder       -> compare counts against physical motion
CAN/FDCAN     -> verify frames, bitrate and bus state
Low power     -> verify wake source and current behavior
```

Use measured evidence when hardware access exists.

If hardware is unavailable, clearly separate static verification from runtime verification.

---

## 19. Completion Report

At completion provide a compact report:

```text
Goal
Target MCU / board
Files changed
Peripherals affected
Clock assumptions verified
Build result
Runtime verification
Remaining warnings / hardware checks
```

Do not claim success beyond the evidence available.
