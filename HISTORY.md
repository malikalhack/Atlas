# Atlas — Project History

Milestone log for the Atlas project.
Records key decisions, structural changes, and completed phases.

---

## 2026-06-29

### Project inception
- Revived a shelved concept: hub and user interface for Psychrometer sensors.
- Concept reviewed; Atlas concentrates readings and may forward to a local
  server (not a replacement for Psychrometer nodes).

### Concept decisions
- **Hub MCU:** STM32F103C8T6, bare registers, no HAL.
- **Toolchain:** CMSIS-Toolbox.
- **Radio:** nRF24L01+ as PRX; 6 pipes, node id in payload for up to 20 nodes.
- **Display:** BC2004BGPLCH6 20x4 HD44780, 4-bit, write-only.
- **Auto bright/contrast:** BH1750 ambient light drives backlight and contrast
  PWM with hysteresis.
- **Uplink:** ENC28J60 (SPI dev board) toward a local server, behind an
  interface so core function works without it.
- **Scheduler:** AcroSched 1.7 cooperative dispatcher, shipped as a prebuilt
  library + headers (sources private). Frozen config: MAX_TASKS=12, watchdog,
  timers (MAX_TIMERS=4), IPC and idle hook on; introspection/stats/hooks off.

### Documentation
- `README.md`, `requirements.md`, `HISTORY.md`, `CHANGELOG.md` created.
- Directory structure agreed: `inc/`, `src/`, `port/`, `docs/`.
