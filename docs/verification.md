# APB Subsystem Verification Plan

## 1. Verification Objectives

The objective of verification is to ensure that the APB Subsystem satisfies all functional requirements defined in the project specification.

Verification shall confirm correct functionality through simulation, self-checking SystemVerilog testbenches, assertions, waveform analysis, synthesis, and static timing analysis.

---

## 2. Verification Methodology

Verification follows a layered approach consisting of:

- Directed testing
- Self-checking SystemVerilog testbenches
- Immediate SystemVerilog assertions
- Waveform analysis
- Generic RTL synthesis using Yosys
- Technology-mapped synthesis using Sky130 HDLL
- Static timing analysis using OpenSTA

Verification shall be performed at both module level and subsystem level.

---

## 3. Verification Environment

| Tool | Use |
|------|-----|
| Icarus Verilog | RTL simulation |
| GTKWave | Waveform viewing |
| Yosys | Generic RTL synthesis |
| Yosys + Sky130 HDLL | Technology mapping |
| OpenSTA | Static timing analysis |

---

## 4. Module Verification

### 4.1 APB Master

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 4.2 APB Interconnect

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 4.3 UART APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 4.4 SPI APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 4.5 I²C APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 4.6 Top-Level Subsystem

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

## 5. Functional Test Cases

The following test cases shall be implemented as self-checking SystemVerilog testbenches.

### 5.1 APB Master

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 5.2 APB Interconnect

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 5.3 UART APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 5.4 SPI APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 5.5 I²C APB Peripheral

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

### 5.6 Top-Level Subsystem

**To be updated soon.**

#### Test Summary

**To be updated soon.**

---

## 6. Assertions

Immediate SystemVerilog assertions shall be used to verify key subsystem invariants during simulation.

### 6.1 APB Master

**To be updated soon.**

### 6.2 APB Interconnect

**To be updated soon.**

### 6.3 UART APB Peripheral

**To be updated soon.**

### 6.4 SPI APB Peripheral

**To be updated soon.**

### 6.5 I²C APB Peripheral

**To be updated soon.**

### 6.6 Top-Level Subsystem

**To be updated soon.**

---

## 7. Coverage Goals

The verification process aims to:

- Verify all APB Master states and transitions.
- Verify APB SETUP and ACCESS phases.
- Verify read and write transactions.
- Verify wait-state handling through `PREADY`.
- Verify APB control-signal stability during ACCESS.
- Verify all peripheral address windows.
- Verify mapped and unmapped accesses.
- Verify one-hot peripheral selection.
- Verify APB response routing.
- Verify UART register access and TX/RX FIFO operation.
- Verify SPI register access and SPI transaction control.
- Verify I²C register access and I²C transaction control.
- Verify reset behaviour.
- Verify boundary and corner cases.
- Verify end-to-end subsystem operation.

Detailed coverage results:

**To be updated soon.**

---

## 8. Success Criteria

Verification is considered complete when:

- All planned tests pass.
- All assertions pass.
- No simulation errors remain.
- All implemented modules are verified at the required level.
- Generic RTL synthesis completes successfully.
- Technology-mapped synthesis completes successfully.
- Static timing analysis reports no timing violations.

---

## 9. Static Timing Analysis Results

**To be updated soon.**

---

## 10. Future Verification Enhancements

Future versions of the verification environment may include:

- Cocotb
- UVM
- Constrained-random verification
- Functional coverage
- Formal verification
