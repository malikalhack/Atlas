# Atlas — Requirements & Task Backlog

**Project:** Atlas
**Author:** Anton Chernov
**Date:** 06/29/2026
**Version:** 0.1.0

---

## 1. Requirements

### REQ-001 — Star Hub Receiver

Atlas shall receive periodic readings from up to 20 Psychrometer nodes over an
nRF24L01+ star network.

**Acceptance criteria:**
- Hub MCU STM32F103C8T6 + nRF24L01+ in PRX role.
- 6 hardware pipes; node id field in payload identifies up to 20 nodes.
- Auto-ACK with retransmit; each node keeps a unique pipe address.

---

### REQ-002 — Packet Format

A compact fixed payload (<= 32 bytes) shall carry node id, sensor data and a
status/battery field, identical to the Psychrometer node struct.

**Acceptance criteria:**
- Fields: node id, T, H, P, battery/status; documented byte layout.
- Endianness defined; same struct on node and hub.

---

### REQ-003 — Display: BC2004BGPLCH6

Atlas shall show per-node temperature, humidity, pressure and last-seen state
on a 20x4 HD44780 LCD.

**Acceptance criteria:**
- 4-bit interface, R/W tied low (write only).
- Carousel/scroll across nodes (3-4 per screen).
- Stale flag when a node misses N transmit periods.

---

### REQ-004 — Auto Brightness and Contrast

A BH1750 ambient light sensor shall drive backlight and contrast automatically.

**Acceptance criteria:**
- BH1750 read over I2C; lux mapped to PWM duty.
- Backlight via switched PWM; contrast via PWM + RC on V0.
- Hysteresis to avoid flicker.

---

### REQ-005 — Optional Server Uplink

Atlas may concentrate readings toward a local server.

**Acceptance criteria:**
- Uplink isolated behind an interface; absence does not block core function.
- Transport: ENC28J60 (SPI dev board) toward a local server.
- Defined message format for forwarded readings.

---

### REQ-006 — Scheduler: AcroSched 1.7

Task flow shall run on AcroSched, shipped as a prebuilt library plus headers
(sources private). Parameters are compile-time and frozen at build:

**Acceptance criteria:**
- `ACROSCHED_MAX_TASKS = 12`; watchdog, timers (`MAX_TIMERS = 4`), IPC and
  idle hook enabled; introspection/stats/hooks disabled.
- Cortex-M3 lib for AC6 and GCC; app provides SysTick tick and config defines.
- IPC mailbox carries nRF packets from ISR to task; idle hook calls `__WFI()`.

---

### REQ-007 — Bare-register Implementation

The hub shall be implemented with CMSIS-Toolbox using direct register access;
no HAL, no CubeMX.

**Acceptance criteria:**
- Clock, GPIO, SPI1, I2C1, timers/PWM configured via registers.
- Only CMSIS device headers used.

---

### REQ-008 — Multi-platform Structure

Common code shall be MCU-independent; STM32F103 specifics live in the port
layer.

**Acceptance criteria:**
- `inc/` and `src/` contain no MCU-specific registers.
- Port directory selects platform code only.

---

### REQ-009 — No Dynamic Memory

Static allocation only; no `malloc`/`free`.

---

### REQ-010 — Doxygen Documentation

All public headers, types and functions documented with Doxygen tags; `docs/`
holds the Doxyfile and diagrams.

---

## 2. Task Backlog

### Phase 0 — Foundation
- [ ] T001 — Repository structure (`inc/ src/ port/ docs/`)
- [ ] T002 — Requirements, HISTORY, CHANGELOG
- [ ] T003 — CMSIS-Toolbox project skeleton (STM32F103C8T6)

### Phase 1 — Hub bring-up
- [ ] T010 — Clock + GPIO + SysTick (bare registers)
- [ ] T011 — AcroSched lib (.a + headers), SysTick + acroInit
- [ ] T012 — SPI1 + nRF24L01+ PRX, multi-pipe, packet struct
- [ ] T013 — I2C1 + BH1750 driver

### Phase 2 — Display
- [ ] T020 — LCD 4-bit driver, per-node table + stale flag
- [ ] T021 — Carousel/scroll across nodes

### Phase 3 — Auto bright/contrast
- [ ] T030 — Backlight + contrast PWM
- [ ] T031 — BH1750 mapping with hysteresis

### Phase 4 — Server uplink
- [ ] T040 — ENC28J60 driver + uplink task to local server

### Phase 5 — Documentation
- [ ] T050 — Doxygen, schematics, pin map
