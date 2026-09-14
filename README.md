# APB Subsystem

![SystemVerilog](https://img.shields.io/badge/SystemVerilog-RTL-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-In%20Development-yellow)

A reusable, parameterized **SystemVerilog APB Subsystem** integrating an APB Master, APB Interconnect, and memory-mapped UART, SPI, and I²C peripherals.

The subsystem reuses the existing UART, FIFO, SPI, and I²C IP while providing a common APB-based register interface for system-level peripheral integration.

This project follows the structured documentation-driven RTL engineering workflow established during the UART SV Core, FIFO SV Core, SPI SV Core, and I²C SV Core projects.

---

# Objectives

- Design a reusable APB-based peripheral subsystem.
- Implement a synthesizable APB Master and APB Interconnect.
- Integrate UART, SPI, and I²C IP through memory-mapped APB peripheral wrappers.
- Reuse the existing synchronous FIFO IP for UART TX and RX buffering.
- Verify the subsystem using self-checking SystemVerilog testbenches and assertions.
- Perform generic synthesis, Sky130 technology mapping, and static timing analysis.
- Maintain clear documentation throughout development.

---

# Planned Features

## Version 1.0.0

- [ ] Synthesizable APB Master
- [ ] APB Interconnect with static address decoding
- [ ] Memory-mapped UART peripheral
- [ ] Memory-mapped SPI peripheral
- [ ] Memory-mapped I²C peripheral
- [ ] 32-bit APB address and data interfaces
- [ ] Single outstanding APB transaction
- [ ] Unmapped-address error handling
- [ ] UART TX FIFO using Standard FIFO mode
- [ ] UART RX FIFO using FWFT mode
- [ ] Reuse of the existing UART, FIFO, SPI, and I²C IP
- [ ] Self-checking SystemVerilog testbenches
- [ ] Immediate SystemVerilog assertions
- [ ] Generic RTL synthesis using Yosys
- [ ] Sky130 HDLL technology mapping
- [ ] Static timing analysis using OpenSTA

## Future Roadmap

- [ ] Multiple APB Masters
- [ ] APB arbitration
- [ ] Interrupt support
- [ ] DMA support
- [ ] Runtime-programmable UART baud rate
- [ ] SPI FIFOs
- [ ] AHB-to-APB bridge
- [ ] AXI4-Lite-to-APB bridge
- [ ] Formal verification
- [ ] Additional memory-mapped peripherals

---

# Repository Structure

```text
rtl/             Synthesizable SystemVerilog RTL
tb/              Self-checking testbenches
assertions/      Immediate SystemVerilog assertions
constraints/     OpenSTA timing constraints
scripts/         Synthesis and timing scripts
reports/         Synthesis and timing reports
docs/            Project documentation
docs/images/     Architecture, FSM, datapath, waveform and timing figures
```

---

# Documentation

The project documentation is organized into the following documents.

| Document | Description |
|----------|-------------|
| [Specification](docs/specification.md) | Functional requirements, interfaces, address map, register maps, timing requirements, assumptions, and future enhancements. |
| [Architecture](docs/architecture.md) | Design philosophy, module hierarchy, architectural organization, datapath, and control flow. |
| [Implementation](docs/implementation.md) | RTL implementation details, algorithms, design decisions, synthesis, and timing analysis. |
| [Verification](docs/verification.md) | Verification methodology, test cases, assertions, coverage goals, and timing validation. |

---

# Toolchain

| Tool | Purpose |
|------|---------|
| SystemVerilog | RTL Design |
| Icarus Verilog | RTL Simulation |
| GTKWave | Waveform Viewing |
| Yosys | Generic RTL Synthesis |
| Sky130 HDLL | Technology Mapping |
| OpenSTA | Static Timing Analysis |
| Git | Version Control |

---

# Development Workflow

```text
Specification
      ↓
Architecture
      ↓
APB Master
      ↓
APB Interconnect
      ↓
UART Peripheral
      ↓
SPI Peripheral
      ↓
I²C Peripheral
      ↓
Top-Level Integration
      ↓
Verification
      ↓
Generic Synthesis
      ↓
Technology Mapping
      ↓
Static Timing Analysis
      ↓
Final Documentation Review
```

---

# Project Status

- [x] Repository initialized
- [x] Project specification
- [x] Architecture
- [x] Initial documentation
- [ ] APB Master RTL
- [ ] APB Interconnect RTL
- [ ] UART APB Peripheral RTL
- [ ] SPI APB Peripheral RTL
- [ ] I²C APB Peripheral RTL
- [ ] Top-Level Subsystem Integration
- [ ] Self-checking Verification
- [ ] Assertions
- [ ] Generic RTL Synthesis
- [ ] Sky130 Technology Mapping
- [ ] Static Timing Analysis
- [ ] Final Documentation

---

# Results

**To be updated soon.**

---

# License

This project is licensed under the MIT License.
