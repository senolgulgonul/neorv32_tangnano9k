# NEORV32 on Tang Nano 9K — Gowin EDA (Windows)

A minimal port of the [NEORV32 RISC-V soft-core processor](https://github.com/stnolting/neorv32) to the [Sipeed Tang Nano 9K](https://wiki.sipeed.com/hardware/en/tang/Tang-Nano-9K/Nano-9K.html) FPGA board, using **Gowin EDA on Windows** — no Linux, no TCL shell required.

Based on the official [neorv32-setups](https://github.com/stnolting/neorv32-setups/tree/main/gowineda) Gowin EDA example, adapted for the Tang Nano 9K's 27 MHz clock and pin assignments.

---

## Board

| | |
|---|---|
| **FPGA** | Gowin GW1NR-9C |
| **Package** | QN88P |
| **Clock** | 27 MHz onboard oscillator |
| **Board** | Sipeed Tang Nano 9K |

---

## Features

- Pure upstream NEORV32 RTL — no modifications to core files
- Standard NEORV32 bootloader — upload programs via UART
- IMEM: 16 KB, DMEM: 8 KB (internal block RAM)
- UART0, GPIO (6 LEDs), BOOTROM enabled
- Gowin EDA `.gprj` project file — open and build directly in the GUI

---

## Prerequisites

### Hardware
- Sipeed Tang Nano 9K
- USB cable

### Software
- [Gowin EDA](https://www.gowinsemi.com/en/support/download_eda/) (Education or Standard Edition, v1.9.x)
- [NEORV32 repository](https://github.com/stnolting/neorv32) (for RTL source files)
- [RISC-V GCC toolchain](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/releases) (`riscv-none-elf-gcc`) — for compiling software
- [make](https://gnuwin32.sourceforge.net/packages/make.htm) for Windows
- PowerShell (built into Windows) — for uploading programs

---

## Getting Started

### 1. Clone the repositories

```
git clone https://github.com/senolgulgonul/neorv32_tangnano9k.git
git clone https://github.com/stnolting/neorv32.git
```

### 2. Open the project in Gowin EDA

Open `neorv32_tangnano9k/neorv32_tangnano9k.gprj` in Gowin FPGA Designer.

### 3. Add the NEORV32 RTL source files

In the Design panel, add **all `.vhd` files** from:
```
neorv32/rtl/core/
neorv32/rtl/core/mem/       ← use the *.default.vhd files
```

> **Important:** Set the library name to `neorv32` for all these files.
> Select them in the Design panel → right-click → Properties → Library = `neorv32`

### 4. Set the top module

Go to **Project → Configuration → Synthesis** and set Top Module to:
```
neorv32_testsetup_bootloader
```

### 5. Synthesize → Place & Route → Generate Bitstream

Run all three steps in the Process panel.

### 6. Flash the FPGA

Open Gowin Programmer, load `impl/pnr/neorv32_tangnano9k.fs` and program the device.

---

## Uploading Software

Connect a serial terminal (e.g. PuTTY) to the Tang Nano 9K's USB-UART port:

| Setting | Value |
|---|---|
| Baud rate | 19200 |
| Data bits | 8 |
| Stop bits | 1 |
| Parity | None |
| Flow control | None |

You should see the NEORV32 bootloader prompt on reset.

### Compile an example program

```powershell
cd neorv32\sw\example\demo_blink_led
make exe
```

### Upload via PowerShell script

Get the upload script from [neorv32-setups/gowineda/tools](https://github.com/stnolting/neorv32-setups/tree/main/gowineda/tools), then:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\uart_upload.ps1 COM4 path\to\neorv32_exe.bin
```

Replace `COM4` with your actual COM port number.

---

## Pin Assignments

| Signal | Pin | Description |
|---|---|---|
| `clk_i` | 52 | 27 MHz oscillator |
| `rstn_i` | 3 | Reset button (active low) |
| `uart0_txd_o` | 17 | USB-UART TX |
| `uart0_rxd_i` | 18 | USB-UART RX |
| `gpio_o[0]` | 10 | LED 0 |
| `gpio_o[1]` | 11 | LED 1 |
| `gpio_o[2]` | 13 | LED 2 |
| `gpio_o[3]` | 14 | LED 3 |
| `gpio_o[4]` | 15 | LED 4 |
| `gpio_o[5]` | 16 | LED 5 |

---

## Acknowledgements

- [NEORV32](https://github.com/stnolting/neorv32) by Stephan Nolting
- [neorv32-setups](https://github.com/stnolting/neorv32-setups) — official Gowin EDA example and tools

## License

BSD-3-Clause — see [LICENSE](LICENSE) for details.
