# Building RC2040

How to build RC2040 for the Pico (RP2040) and Pico 2 (RP2350), based on `CMakeLists.txt`, `Compiling.md` and the `#define` switches in the source.

> These steps have not been run with a Pico toolchain. Check them on your own machine.

## Prerequisites

```
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential libstdc++-arm-none-eabi-newlib
git clone -b master https://github.com/raspberrypi/pico-sdk.git
cd pico-sdk && git submodule update --init
export PICO_SDK_PATH=/path/to/pico-sdk
```

`pico_sdk_import.cmake` is already in the project. `Compiling.md` says to copy it from the SDK, but that isn't needed.

## Build

```
cd RC2040
mkdir build2040 && cd build2040
cmake -DPICO_PLATFORM=rp2040 .. && make -j4      # Pico
cd .. && mkdir build2350 && cd build2350
cmake -DPICO_PLATFORM=rp2350 .. && make -j4      # Pico 2
```

- **Output:** `RC2040.uf2`, plus `.bin`, `.hex` and `.elf`.

## CMake options

| Option | Values | Notes |
|---|---|---|
| `-DPICO_PLATFORM` | `rp2040` (Pico) or `rp2350` (Pico 2) | The main option. An existing build cache shows `rp2040` as the default. |
| `-DCMAKE_BUILD_TYPE` | `Release`, `Debug`, `RelWithDebInfo`, `MinSizeRel` | Unset gives `Release` with the SDK, which is what you want for speed. |
| `-DPICO_SDK_PATH` | path | Alternative to the environment variable. |
| `-DPICO_SDK_FETCH_FROM_GIT=ON` | on/off | Downloads the SDK if no path is set. `PICO_SDK_FETCH_FROM_GIT_PATH` sets where it goes. |
| `-DPICO_BOARD` | e.g. `pico`, `pico2` | Optional. An existing build cache shows `pico`. |

Use a fresh build directory when you change `PICO_PLATFORM`, because CMake remembers the old value. Any shipped `build/` folder holds a cache pointing at a different SDK path, so delete it rather than reusing it.

## Fixed in `CMakeLists.txt`

- Language standards are C11 and C++17.
- `-Wl,--print-memory-usage` is set, so the build prints RAM and flash use.
- USB and UART stdio are both enabled.
- `PICO_STDIO_USB_ENABLE_RESET_VIA_BAUD_RATE=1` is set, so a 1200 baud connection resets the board into the bootloader.
- `PICO_STDIO_USB_ENABLE_RESET_VIA_VENDOR_INTERFACE=0` is set.
- Libraries linked: `pico_multicore`, `pico_stdlib`, `hardware_gpio`, `hardware_pwm`, `hardware_pio`, `hardware_dma`, `hardware_irq`, `hardware_flash`, `hardware_spi`, `pico_fatfs` and `libz80`.

## Source switches

These are edited in the source, not passed to CMake.

| Switch | File | Default | Effect |
|---|---|---|---|
| `ENABLEFFS` | `RC2040.c` | defined | Enables serial file transfer from the AUX button. Comment it out to remove it. |
| `SD_PIN_MISO`, `SD_PIN_CS`, `SD_PIN_SCK`, `SD_PIN_MOSI` | `RC2040.c` | 16, 17, 18, 19 | SD card pins on SPI0. See `GPIO settings.md`. |

## Local changes to be aware of

- **SD card:** the pins are set with `pico_fatfs_set_config()` just before `f_mount()`.
- **8-bit port:** `PIOAp[]` is `{2,3,4,5,20,21,26,27}`, so port bits 0-3 are on GPIO2-5 because GPIO16-19 are used by the SD card.
- **Speed:** `libz80` is compiled with `-Os`, and its hot code and the memory callbacks are placed in RAM. If the Pico 1 build fails with "RAM overflowed", trim what goes into RAM.
- **`.pio_version 0`:** RC2040 already ships a Pico 2 UF2 without this line in `spi.pio`, so it is optional.

## At run time

The SD card needs the files from the project's `SD Card Contents` folder, including `rc2040.ini`.
