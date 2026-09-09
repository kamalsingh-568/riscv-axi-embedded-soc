# AXI4-Lite Protocol

**Project:** RISC-V AXI4-Lite SoC (PicoRV32-based)
**Stage:** 02 — SoC Design Verification
**Reference RTL:** `axi_lite_interconnect.v`, `axi_decoder.v`

---

## 1. Overview

AXI4-Lite is used as the memory-mapped communication interface between the processor subsystem and the peripheral/memory interfaces (ROM, SRAM, UART) in this SoC.

It provides independent read and write channels, each governed by a simple VALID/READY handshake, and is a reduced (single-transfer, no burst) subset of the full AXI4 protocol — well suited to simple memory-mapped peripherals like the ones in this design.

---

## 2. AXI4-Lite Channels

AXI4-Lite consists of five independent channels, split across the write and read paths.

### 2.1 Write Address Channel (AW)

Transfers the address associated with a write transaction.

| Signal | Description |
|---|---|
| `AWADDR` | Write address |
| `AWVALID` | Master signals the address is valid |
| `AWREADY` | Slave signals it is ready to accept the address |

### 2.2 Write Data Channel (W)

Transfers the write data.

| Signal | Description |
|---|---|
| `WDATA` | Write data |
| `WSTRB` | Byte-lane strobe, indicates valid bytes within `WDATA` |
| `WVALID` | Master signals the data is valid |
| `WREADY` | Slave signals it is ready to accept the data |

### 2.3 Write Response Channel (B)

Returns the response for a completed write transaction.

| Signal | Description |
|---|---|
| `BRESP` | Write response status (e.g., OKAY / SLVERR) |
| `BVALID` | Slave signals the response is valid |
| `BREADY` | Master signals it is ready to accept the response |

### 2.4 Read Address Channel (AR)

Transfers the address of a read transaction.

| Signal | Description |
|---|---|
| `ARADDR` | Read address |
| `ARVALID` | Master signals the address is valid |
| `ARREADY` | Slave signals it is ready to accept the address |

### 2.5 Read Data Channel (R)

Returns the requested data along with the response status.

| Signal | Description |
|---|---|
| `RDATA` | Read data |
| `RRESP` | Read response status (e.g., OKAY / SLVERR) |
| `RVALID` | Slave signals the data/response is valid |
| `RREADY` | Master signals it is ready to accept the data |

---

## 3. VALID/READY Handshake

An AXI4-Lite transfer occurs on any clock edge where both `VALID` and `READY` are asserted simultaneously, on the same channel. Either side may hold its signal until the other is ready — the source asserting `VALID` and waiting for `READY`, or the destination asserting `READY` in advance.

```text
        Clock edge
             |
             v
      VALID = 1 AND READY = 1 ?
             |
      ------------------
      |                |
      No                Yes
      |                |
      v                v
  Wait (hold state)   Transfer occurs
                        (data/address/response
                         captured this cycle)
```

Each of the five channels performs this handshake independently, which is what allows, for example, the write address and write data to arrive on different cycles while still completing a single write transaction.

---

## 4. Transaction Flow

### 4.1 Write Transaction

1. Master asserts `AWADDR` + `AWVALID` (Write Address Channel handshake with slave's `AWREADY`).
2. Master asserts `WDATA`, `WSTRB` + `WVALID` (Write Data Channel handshake with slave's `WREADY`) — may occur in parallel with step 1.
3. Once both address and data are accepted, the slave asserts `BVALID` with `BRESP`; transaction completes when master asserts `BREADY`.

### 4.2 Read Transaction

1. Master asserts `ARADDR` + `ARVALID` (Read Address Channel handshake with slave's `ARREADY`).
2. Slave returns `RDATA` + `RRESP` with `RVALID`; transaction completes when master asserts `RREADY`.

---

## 5. Relevance to This SoC

In this design, the AXI4-Lite interconnect (`axi_lite_interconnect.v`) sits between the PicoRV32 core and the address decoder (`axi_decoder.v`), which routes each transaction to ROM, SRAM, or the UART peripheral based on the decoded address — as detailed in the Memory Map document. Interface-level verification of these five channels and the handshake behavior is covered under Section 3.2 of the Verification Plan.
VALID = 1
READY = 1
    |
    v
Transfer occurs
