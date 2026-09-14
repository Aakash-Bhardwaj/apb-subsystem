# APB Subsystem Implementation

## 1. Overview

This document describes the implementation details of the APB Subsystem.

It complements the project specification and architecture documents by documenting the RTL organization, coding style, implementation decisions, design trade-offs, synthesis results, and timing analysis throughout the development of the project.

---

## 2. Coding Guidelines

The APB Subsystem follows the implementation guidelines below:

- SystemVerilog is used throughout the project.
- Only synthesizable RTL constructs are used for the design.
- Sequential logic is implemented using `always_ff`.
- Combinational logic is implemented using `always_comb`.
- Non-blocking assignments (`<=`) are used for sequential logic.
- Blocking assignments (`=`) are used for combinational logic.
- Enumerated types are used for finite-state machines where applicable.
- Parameters are validated during elaboration whenever possible.
- The design operates entirely within a single clock domain.
- Clock-enable signals are preferred over internally generated clocks where applicable.
- The design is fully parameterized wherever practical.
- Vendor-specific constructs are avoided wherever possible.

---

# 3. APB Master

## 3.1 Module Overview

**To be updated soon.**

---

## 3.2 Interface

### Parameters

**To be updated soon.**

### Inputs

**To be updated soon.**

### Outputs

**To be updated soon.**

---

## 3.3 Internal Registers

**To be updated soon.**

---

## 3.4 Combinational Signals

**To be updated soon.**

---

## 3.5 Datapath & State Machine

**To be updated soon.**

---

## 3.6 Algorithm

**To be updated soon.**

---

## 3.7 Design Decisions

**To be updated soon.**

---

## 3.8 Corner Cases

**To be updated soon.**

---

## 3.9 Resource Utilization

### Synthesis Results

**To be updated soon.**

### Cell Breakdown

**To be updated soon.**

### Waveform

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 4. APB Interconnect

## 4.1 Module Overview

**To be updated soon.**

---

## 4.2 Interface

**To be updated soon.**

---

## 4.3 Address Decoder

**To be updated soon.**

---

## 4.4 Request Routing

**To be updated soon.**

---

## 4.5 Response Routing

**To be updated soon.**

---

## 4.6 Design Decisions

**To be updated soon.**

---

## 4.7 Corner Cases

**To be updated soon.**

---

## 4.8 Resource Utilization

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 5. UART APB Peripheral

## 5.1 Module Overview

**To be updated soon.**

---

## 5.2 Interface

**To be updated soon.**

---

## 5.3 Register Interface

**To be updated soon.**

---

## 5.4 TX FIFO

**To be updated soon.**

---

## 5.5 RX FIFO

**To be updated soon.**

---

## 5.6 Datapath & Control

**To be updated soon.**

---

## 5.7 Algorithm

**To be updated soon.**

---

## 5.8 Design Decisions

**To be updated soon.**

---

## 5.9 Corner Cases

**To be updated soon.**

---

## 5.10 Resource Utilization

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 6. SPI APB Peripheral

## 6.1 Module Overview

**To be updated soon.**

---

## 6.2 Interface

**To be updated soon.**

---

## 6.3 Register Interface

**To be updated soon.**

---

## 6.4 Datapath & Control

**To be updated soon.**

---

## 6.5 Algorithm

**To be updated soon.**

---

## 6.6 Design Decisions

**To be updated soon.**

---

## 6.7 Corner Cases

**To be updated soon.**

---

## 6.8 Resource Utilization

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 7. I²C APB Peripheral

## 7.1 Module Overview

**To be updated soon.**

---

## 7.2 Interface

**To be updated soon.**

---

## 7.3 Register Interface

**To be updated soon.**

---

## 7.4 Datapath & Control

**To be updated soon.**

---

## 7.5 Algorithm

**To be updated soon.**

---

## 7.6 Design Decisions

**To be updated soon.**

---

## 7.7 Corner Cases

**To be updated soon.**

---

## 7.8 Resource Utilization

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 8. Top-Level Integration

## 8.1 Module Overview

**To be updated soon.**

---

## 8.2 Interface

**To be updated soon.**

---

## 8.3 Module Integration

**To be updated soon.**

---

## 8.4 Parameter Propagation

**To be updated soon.**

---

## 8.5 Reset Integration

**To be updated soon.**

---

## 8.6 Design Decisions

**To be updated soon.**

---

## 8.7 Corner Cases

**To be updated soon.**

---

## 8.8 Resource Utilization

**To be updated soon.**

### Verification Status

- [ ] RTL Simulation
- [ ] Self-checking Testbench
- [ ] Assertions
- [ ] Generic Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis

---

# 9. Synthesis

## 9.1 Generic RTL Synthesis

**To be updated soon.**

---

## 9.2 Sky130 Technology Mapping

**To be updated soon.**

---

## 9.3 Resource Summary

**To be updated soon.**

---

# 10. Static Timing Analysis

**To be updated soon.**

---

# 11. Implementation Summary

**To be updated soon.**
