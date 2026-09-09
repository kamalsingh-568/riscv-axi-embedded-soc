
---

# 4. `verification_plan.md`

```markdown
# Verification Plan

## 1. Objective

The objective of verification is to confirm the functional correctness of the RISC-V AXI4-Lite SoC at RTL before physical implementation.

The verification process focuses on processor execution, memory access, AXI4-Lite communication, address decoding, UART functionality, and system-level integration.

## 2. Verification Environment

The RTL verification environment consists of:

- Device Under Test (DUT)
- PicoRV32 processor
- ROM
- SRAM
- AXI4-Lite interconnect
- AXI address decoder
- UART peripheral
- UART transmitter
- UART receiver
- Clock and reset generation
- Firmware image

## 3. Verification Strategy

Verification is performed at multiple levels.

### 3.1 Module-Level Verification

Individual RTL blocks are checked for:

- Correct reset behavior
- Expected input/output behavior
- Sequential operation
- Combinational logic
- Interface connectivity

### 3.2 Interface-Level Verification

AXI4-Lite communication is checked for:

- Address transfer
- Data transfer
- VALID/READY handshaking
- Read response
- Write response

### 3.3 System-Level Verification

The complete SoC is verified by executing firmware and observing processor communication with memories and peripherals.

## 4. Test Scenarios

| Test Scenario | Verification Objective | Expected Result |
|---|---|---|
| Reset | Verify initialization | SoC enters a known reset state |
| Firmware Execution | Verify processor startup | PicoRV32 executes firmware |
| ROM Access | Verify instruction fetch | Correct firmware data is read |
| SRAM Write | Verify write operation | Data is stored correctly |
| SRAM Read | Verify read operation | Correct data is returned |
| AXI-Lite Write | Verify write channel | Write transaction completes |
| AXI-Lite Read | Verify read channel | Correct read data is returned |
| Address Decode | Verify slave selection | Correct target is selected |
| UART TX | Verify transmission | Expected UART output is generated |
| UART RX | Verify reception | Received data is handled correctly |
| SoC Integration | Verify complete system | Integrated system operates correctly |

## 5. Waveform Verification

Waveform analysis is used to trace internal signals and verify transaction-level behavior.

Important signals include:

- Clock
- Reset
- Processor bus signals
- AXI-Lite address signals
- AXI-Lite data signals
- `VALID`
- `READY`
- Read response signals
- Write response signals
- UART signals

Waveform captures are stored in the `waveforms/` directory.

## 6. Functional Verification

Functional verification confirms that:

1. The processor exits reset correctly.
2. Firmware is fetched from ROM.
3. Memory-mapped accesses are generated correctly.
4. Address decoding selects the intended slave.
5. AXI4-Lite transactions complete correctly.
6. Read and write data are transferred correctly.
7. UART accesses operate correctly.
8. The integrated SoC performs the intended operations.

## 7. Verification Evidence

The repository contains supporting verification artifacts including:

- RTL source files
- Testbench files
- Simulation waveforms
- AXI-Lite transaction visualization
- UART verification captures
- Firmware execution evidence
- Simulation results

## 8. RTL-to-GDSII Handoff

After functional verification, the verified RTL was used as the input to the physical implementation flow.

The RTL-to-GDSII flow includes:

- Synthesis
- Floorplanning
- Placement
- Clock Tree Synthesis
- Routing
- Static Timing Analysis
- DRC
- LVS
- GDSII generation

## 9. Verification Status

| Verification Area | Status |
|---|---|
| Processor Integration | Completed |
| Firmware Execution | Completed |
| ROM Access | Completed |
| SRAM Access | Completed |
| AXI4-Lite Communication | Completed |
| Address Decoding | Completed |
| UART Integration | Completed |
| Waveform Verification | Completed |
| RTL-to-GDSII Handoff | Completed |
