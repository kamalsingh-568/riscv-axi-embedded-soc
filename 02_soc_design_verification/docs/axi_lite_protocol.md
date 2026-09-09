
---

# 3. `axi_lite_protocol.md`

```markdown
# AXI4-Lite Protocol

## 1. Overview

AXI4-Lite is used as the memory-mapped communication interface between the processor subsystem and peripheral/memory interfaces.

AXI4-Lite provides independent read and write channels using a VALID/READY handshake mechanism.

## 2. AXI4-Lite Channels

AXI4-Lite consists of five independent channels.

### Write Address Channel

Transfers the address associated with a write transaction.

Key signals include:

- `AWADDR`
- `AWVALID`
- `AWREADY`

### Write Data Channel

Transfers write data.

Key signals include:

- `WDATA`
- `WSTRB`
- `WVALID`
- `WREADY`

### Write Response Channel

Returns the response associated with a completed write transaction.

Key signals include:

- `BRESP`
- `BVALID`
- `BREADY`

### Read Address Channel

Transfers the address of a read transaction.

Key signals include:

- `ARADDR`
- `ARVALID`
- `ARREADY`

### Read Data Channel

Returns the requested data and response status.

Key signals include:

- `RDATA`
- `RRESP`
- `RVALID`
- `RREADY`

## 3. VALID/READY Handshake

An AXI4-Lite transfer occurs when both `VALID` and `READY` are asserted during the same clock cycle.

```text
VALID = 1
READY = 1
    |
    v
Transfer occurs
