# ThermoLamp firmware installer

Browser-based installer for the full-size ThermoLamp (Waveshare ESP32-S3-Touch-LCD-2.8C), served by GitHub Pages:
**https://owenhollingsworth.github.io/thermolamp/**

This repo holds only the installer page and compiled firmware, not the source.

## Files

| Path | What it is | Flash offset |
|---|---|---|
| `firmware/bootloader.bin` | Second-stage bootloader | `0x0` |
| `firmware/partitions.bin` | Partition table (16 MB, 3 MB app ×2, FATFS) | `0x8000` |
| `firmware/boot_app0.bin` | OTA data (boots `app0`) | `0xE000` |
| `firmware/thermolamp.bin` | ThermoLamp app | `0x10000` |
| `manifest.json` | Tells ESP Web Tools what to flash | |
| `vendor/esp-web-tools/` | ESP Web Tools 10.5.2 (Apache 2.0), hosted here so the page has no CDN dependency | |

The parts are flashed separately on purpose, not as Arduino's 16 MB `.merged.bin`. The merged image covers the NVS area at `0x9000`, so flashing it would wipe every lamp's saved cities and settings on each update.

## Publishing a new firmware release

1. In Arduino IDE (board package 3.0.2 **with Waveshare's XIP libraries**, OPI PSRAM, QIO 80 MHz, 16 MB, *16M Flash (3MB APP/9.9MB FATFS)*, USB Mode *Hardware CDC and JTAG*), run **Sketch → Export Compiled Binary**. These Tools settings reset when the board is re-detected, so check them before every release.
2. From `firmware/ThermoLamp/build/esp32.esp32.esp32s3/`, copy:
   * `ThermoLamp.ino.bin` → `firmware/thermolamp.bin`
   * `ThermoLamp.ino.bootloader.bin` → `firmware/bootloader.bin`
   * `ThermoLamp.ino.partitions.bin` → `firmware/partitions.bin` (only changes if the partition scheme changes)
3. Bump `"version"` in `manifest.json` to match `FW_VERSION` in `config.h` (the installer compares them to offer "Update ThermoLamp").
4. Commit and push. Pages republishes in about a minute.

`boot_app0.bin` doesn't change between builds.
