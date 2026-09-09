# RISC-V AXI4-Lite Embedded SoC

**A complete RTL-to-GDSII System-on-Chip built around a PicoRV32 RISC-V core, an AXI4-Lite interconnect, and a memory-mapped UART peripheral — from firmware to a signed-off SKY130 layout.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Core](https://img.shields.io/badge/Core-PicoRV32%20(RV32I)-blue)
![Interconnect](https://img.shields.io/badge/Bus-AXI4--Lite-blue)
![PDK](https://img.shields.io/badge/PDK-SkyWater%20SKY130-orange)
![Flow](https://img.shields.io/badge/Flow-OpenLane%20%2F%20OpenROAD-orange)
![Status](https://img.shields.io/badge/GDSII-Generated-success)

---

## Overview

This project implements a small, self-contained embedded SoC end-to-end: a RISC-V processor executes real firmware, talks to memory and a UART peripheral over an industry-standard AXI4-Lite bus, and the whole design is carried all the way through RTL functional verification and physical implementation into a manufacturable GDSII layout on the open-source SKY130 process.

It is organized as three sequential stages, each buildable and reviewable on its own:

| Stage | Folder | What it covers |
|---|---|---|
| 1 | [`01_firmware/`](01_firmware) | C firmware, cross-compile toolchain, ROM image generation |
| 2 | [`02_soc_design_verification/`](02_soc_design_verification) | RTL design, Verilator testbenches, waveform-based functional verification |
| 3 | [`03_rtl_to_gdsii/`](03_rtl_to_gdsii) | OpenLane/OpenROAD physical implementation, signoff reports, final GDSII |

---

## System Architecture

```text
                 PicoRV32 (RV32I)
                        |
                        v
                 AXI4-Lite Master
                        |
                        v
               AXI4-Lite Interconnect
                        |
                        v
                 Address Decoder
                /       |        \
               /        |         \
              v         v          v
            ROM       SRAM       UART
          (fetch)   (data R/W)  (memory-mapped,
                                  0x1000_0000)
```

- **PicoRV32** — a compact, open-source RV32I core, executing firmware loaded into on-chip ROM.
- **AXI4-Lite interconnect + decoder** — routes every processor transaction to the correct slave and manages the VALID/READY handshake on all five AXI channels.
- **ROM / SRAM** — instruction storage (initialized from the compiled firmware image) and runtime data memory.
- **UART peripheral** — memory-mapped TX/RX/status registers, verified with an end-to-end echo firmware test.

Full architectural detail lives in [`docs/architecture.md`](02_soc_design_verification/docs/architecture.md).

---

## Repository Structure

```text
riscv-axi-embedded-soc/
├── 01_firmware/                  # Firmware source, build system, compiled outputs
│   ├── src/                      # crt0.S, main.c, linker script, bin2hex.py
│   └── build/                    # firmware.elf, firmware.bin, rom.hex
│
├── 02_soc_design_verification/   # RTL + functional verification
│   ├── rtl/                      # picorv32.v, axi_lite_interconnect.v, axi_decoder.v,
│   │                              # rom.v, sram.v, uart_axi.v, uart_tx.v, uart_rx.v, top.v
│   ├── tb/                       # Verilator C++/Verilog testbenches
│   ├── waveforms/                # Captured simulation waveforms
│   ├── recording/                # Build & waveform-tracing screen recordings
│   ├── images/                   # Architecture, AXI transaction, and UART diagrams
│   └── docs/                     # architecture.md, axi_lite_protocol.md,
│                                  # memory_map.md, verification_plan.md
│
├── 03_rtl_to_gdsii/               # Physical implementation (OpenLane / SKY130)
│   ├── src/                       # RTL snapshot used for the signed-off run
│   ├── config/                    # config.json, top.sdc, pin_order.cfg
│   ├── reports/                   # synthesis / placement / CTS / routing / signoff reports
│   ├── results/                   # final_netlist.v + results README
│   └── images/                    # floorplan, placement, CTS, routing, DRC/LVS, GDS views
│
└── LICENSE
```

---

## Design Flow

1. **Firmware** is written in C, cross-compiled with a bare-metal `riscv32-unknown-elf` toolchain, and converted into `rom.hex` for the RTL ROM to load.
2. **RTL & verification** — the SoC RTL is exercised with Verilator-based C++ testbenches, covering module-level, AXI4-Lite interface-level, and full system-level scenarios; see the [Verification Plan](02_soc_design_verification/docs/verification_plan.md).
3. **Physical implementation** — the verified RTL is carried through the OpenLane/OpenROAD flow on `sky130_fd_sc_hd`: synthesis → floorplanning → placement → CTS → routing → STA → DRC/LVS → GDSII.

---

## Implementation Results

Signed-off physical implementation on **SKY130 (`sky130_fd_sc_hd`)**, target clock **50 MHz (20 ns period)**:

| Metric | Result |
|---|---|
| Flow status | ✅ Completed |
| Die area | 1.00 mm² |
| Core utilization | 35% |
| Total cells | 24,160 |
| Critical path delay | 1.95 ns |
| Setup WNS / TNS | 0.00 ns / 0.00 ns |
| Routing violations | 0 |
| LVS errors | 0 |
| Pin / net antenna violations | 11 / 11 |

The setup timing meets the 20 ns constraint with zero negative slack, and the design completed DRC and LVS with zero reported violations. Antenna violations remain open and are flagged for a further physical-design iteration — see the full breakdown in the [RTL-to-GDSII Final Summary](03_rtl_to_gdsii/reports/final_summary.md) and [Results README](03_rtl_to_gdsii/results/README.md).

---

## Documentation

Detailed, topic-specific documentation lives alongside the RTL rather than being duplicated here:

| Document | Description |
|---|---|
| [Architecture](02_soc_design_verification/docs/architecture.md) | Block-level system architecture and signal flow |
| [AXI4-Lite Protocol](02_soc_design_verification/docs/axi_lite_protocol.md) | Channel definitions, handshake behavior, transaction flow |
| [Memory Map](02_soc_design_verification/docs/memory_map.md) | Address regions for ROM, SRAM, and UART |
| [Verification Plan](02_soc_design_verification/docs/verification_plan.md) | Verification strategy, test scenarios, and status |
| [RTL-to-GDSII Final Summary](03_rtl_to_gdsii/reports/final_summary.md) | Full physical implementation and signoff report |

---

## Tools & Technologies

| Category | Tools |
|---|---|
| RTL / Simulation | Verilog HDL, Verilator, GTKWave |
| Firmware | `riscv32-unknown-elf-gcc`, RV32I |
| Physical Implementation | Yosys, OpenLane, OpenROAD, OpenSTA |
| Layout / Signoff | Magic, KLayout |
| PDK | SkyWater SKY130 (`sky130_fd_sc_hd`) |
| Environment | Linux / Ubuntu |

---

## Getting Started

```bash
# 1. Build firmware and generate the ROM image
cd 01_firmware
make

# 2. Run RTL functional verification (Verilator)
cd ../02_soc_design_verification/tb
# build & run the relevant testbench, e.g.
verilator --cc --exe --build tb_top.cpp tb_top.v -I../rtl
./obj_dir/Vtb_top

# 3. Physical implementation (inside an OpenLane environment)
cd ../../03_rtl_to_gdsii
flow.tcl -design . -tag my_run
```

> Exact commands depend on your local Verilator/OpenLane setup — see each stage's own docs for details.

---

## License

Released under the [MIT License](LICENSE).

## Author

**Kamal Singh** — Final-year ECE student, focused on analog/mixed-signal and ASIC physical design.
