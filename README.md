# Voron V0.1 - "UNIT-01"

Klipper configuration for a Voron V0.1 120mm CoreXY 3D printer.

## Hardware

| Component | Detail |
|---|---|
| Frame | Voron V0.1 120mm |
| Controller | BTT SKR Mini E3 V2.0 (STM32F103) |
| Drivers | TMC2209 (UART) |
| Toolhead | Dragon Burner (Sherpa Micro + Phaetus Dragon) |
| Nozzle | Hardened steel, 0.4mm |
| Homing | Sensorless homing X/Y (StallGuard), endstop Z |
| Display | Waveshare 12864 OLED (STM32F042 MCU) |
| Aux fan | 5V PWM fan via IRLZ44N MOSFET on RPi GPIO17 |
| Nozzle brush | Servo-actuated (PA8) |
| Bed plates | Smooth PEI, Textured PEI, Carbon Holo |
| Web UI | Mainsail |

## Config Structure

```
printer.cfg          # Main config (steppers, heaters, fans, sensors, input shaper)
macros.cfg           # PRINT_START, PRINT_END, _CG28, CLEAN_NOZZLE, filament macros, M109/M190 overrides
homing.cfg           # Sensorless homing override (_HOME_X, _HOME_Y) with current reduction
V0Display.cfg        # Waveshare 12864 display and status neopixel
aux_fan.cfg          # Auxiliary fan (RPi GPIO17) with M106/M107 P2 override
bed_mesh.cfg         # Bed mesh config (static, no probe - manually measured)
config_backup.cfg    # Shell command macro for GitHub auto-backup
adxlmcu.cfg          # ADXL345 accelerometer on RP2040 MCU (disabled, for resonance testing)
test_speed.cfg       # TEST_SPEED macro for checking stepper skipping
mainsail.cfg         # Mainsail UI macros
timelapse.cfg        # Timelapse integration
crowsnest.conf       # Webcam config
moonraker.conf       # Moonraker API server config
autocommit.sh        # Auto-backup script (pushes to GitHub)
```

## Key Features

- **Sensorless homing** on X/Y with reduced motor current during homing for reliability
- **Servo nozzle brush** for automatic nozzle cleaning before prints
- **Custom M109/M190 overrides** with tight temperature tolerance (within 1C)
- **Conditional homing** (_CG28) skips re-homing when already homed
- **Static bed mesh** loaded from saved profile (no bed probe)
- **Input shaper** tuned via ADXL345 (X: EI @ 97.4Hz, Y: MZV @ 64.6Hz)
- **Auto-backup** to GitHub with merge conflict handling
- **Timelapse** support via Moonraker

## Print Start Sequence

1. Start heating bed and nozzle
2. Conditional home (_CG28)
3. If bed > 90C: fan-assisted heatsoak, wait for bed temp
4. Otherwise: move to centre, wait for bed temp
5. Wait for hotend temp
6. Clean nozzle (servo brush), then prime 5mm
7. Load saved bed mesh
8. Start printing
