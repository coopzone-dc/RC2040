# RC2040
This is an optimised version of the original project by ExtremeElectronics, see https://github.com/ExtremeElectronics/RC2040 for the original.

The optimisation was done my me and an AI! Changes that have been made are:

# RC2040: speed optimisation notes

## Changes in `libz80`

- **RAM placement:** the main-opcode handlers, the helper functions and the execute loop now run from RAM instead of flash.
- **Opcode table:** the main opcode table is now in RAM too (4 KB).
- **Compiler flag:** `libz80` is compiled with `-Os` to keep the RAM footprint small.

## Changes in `RC2040.c`

- **Trace hook:** `z80_trace` was being called for every Z80 instruction even with tracing off. It is now only hooked in while `TRACE_CPU` is enabled, and the main loop refreshes that each pass.
- **Memory callbacks:** the five memory read/write callbacks (`mem_read0`, `mem_write0`, `do_mem_read`, `mem_read`, `mem_write`) now run from RAM.

## SD-CARD changes

The GPIO pins used for the SD-CARD have changed in this version, it now uses the same as the RunCPM project so the same setup can run any of the RunCPM, RC2040 or my version of RC2040-ROMWBW all without changing the SD-CARD connections.

Note: This change will prevent it running on the original board from extreme electronics without changing the config for the SD-CARD gpio pins. The pins are defined near the top of RC2040.c Also see GIPO setting.md


## Z80 CP/M 80 emulation of RC2014 using the RP2040 (PI PICO) or RP2350 (PI PICO2) processor.
## and emulation of a handfull of hardware peripherals. 
