
---

# 2. `memory_map.md`

```markdown
# Memory Map

## 1. Overview

The SoC uses a memory-mapped architecture in which processor accesses to memories and peripherals are identified by their address ranges.

The AXI address decoder uses the incoming address to determine which slave device should receive the transaction.

## 2. Address Mapping

The address ranges used by the SoC are defined by the address-decoding logic implemented in the RTL.

| Address Region | Device | Access | Description |
|---|---|---|---|
| Defined in RTL | ROM | Read | Firmware/program storage |
| Defined in RTL | SRAM | Read/Write | Data storage |
| Defined in RTL | UART | Read/Write | Memory-mapped serial peripheral |

> The exact address values are defined by the RTL address decoder and should remain consistent with `axi_decoder.v` and the SoC top-level integration.

## 3. ROM

ROM is used to store the firmware executed by the PicoRV32 processor.

### Function

- Stores program instructions
- Provides instruction data to the processor
- Initialized using the generated hexadecimal firmware image

### Access

ROM is primarily accessed through processor read operations.

## 4. SRAM

SRAM provides read/write storage for processor data.

### Function

- Runtime data storage
- Processor read operations
- Processor write operations

## 5. UART

UART is integrated as a memory-mapped peripheral.

The processor communicates with the UART by generating read and write transactions to the UART address region.

### Function

- Transmit data
- Receive data
- Provide UART status/control through the implemented register interface

## 6. Address Decoding

The address decoder examines the processor-generated address and selects the appropriate slave.

```text
             Processor Address
                     |
                     v
              Address Decoder
               /      |      \
              /       |       \
             v        v        v
            ROM      SRAM     UART
