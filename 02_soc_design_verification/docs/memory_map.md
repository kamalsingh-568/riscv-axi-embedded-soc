# Memory Map

**Project:** RISC-V AXI4-Lite SoC (PicoRV32-based)
**Stage:** 02 — SoC Design Verification
**Reference RTL:** `axi_decoder.v`, `top.v`

---

## 1. Overview

The SoC uses a memory-mapped architecture in which processor accesses to memories and peripherals are identified by their address ranges.

The AXI address decoder inspects the incoming address on every transaction and determines which slave device — ROM, SRAM, or UART — should receive it.

---

## 2. Address Mapping

The address ranges below are defined by the address-decoding logic implemented in the RTL. Values must be kept consistent with `axi_decoder.v` and the SoC top-level integration (`top.v`).

| Address Region | Device | Access | Description |
|---|---|---|---|
| `0x00000000 – <TBD>` | ROM | Read | Firmware / program storage |
| `<TBD>` | SRAM | Read / Write | Data storage |
| `<TBD>` | UART | Read / Write | Memory-mapped serial peripheral |

> **Note:** Replace the `<TBD>` placeholders with the exact base addresses and ranges from `axi_decoder.v` before final commit, so the table stays a single source of truth for the memory map.

---

## 3. ROM

ROM stores the firmware executed by the PicoRV32 processor.

### Function

- Stores program instructions
- Supplies instruction data to the processor on fetch
- Initialized from the generated hexadecimal firmware image (`rom.hex`)

### Access

ROM is accessed exclusively through processor read operations; it is not writable at runtime.

---

## 4. SRAM

SRAM provides read/write storage for processor data.

### Function

- Runtime data storage (stack, heap, variables)
- Serves processor read operations
- Serves processor write operations

---

## 5. UART

UART is integrated as a memory-mapped peripheral.

The processor communicates with the UART by generating read and write transactions to the UART address region, rather than through dedicated instructions.

### Function

- Transmit data (write to TX register/FIFO)
- Receive data (read from RX register/FIFO)
- Expose UART status and control through the implemented register interface

---

## 6. Address Decoding

The address decoder examines the processor-generated address on every AXI4-Lite transaction and asserts the select signal for the matching slave.

```text
             Processor Address
                     |
                     v
              Address Decoder
               /      |       \
              /       |        \
             v        v         v
            ROM      SRAM      UART
         (Read)   (Read/Write) (Read/Write)
```

Only one slave select line is asserted per transaction; an address that falls outside all defined regions results in no slave being selected (and, depending on RTL implementation, may need to be handled as an error/default response — confirm this behavior against `axi_decoder.v`).
