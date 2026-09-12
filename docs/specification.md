# APB Subsystem Specification

## 1. Introduction

The APB Subsystem is a reusable parameterized SystemVerilog design that integrates an APB Master, an APB Interconnect, and memory-mapped UART, SPI, and I²C peripherals.

The subsystem provides a common APB-based register interface for controlling and monitoring the integrated peripheral IP while maintaining separation between bus infrastructure, peripheral wrappers, and protocol cores.

The design targets:

* FPGA implementation
* ASIC implementation
* Generic synthesis
* Sky130 HDLL technology mapping
* Educational and production-quality reusable IP

This project follows the documentation-driven development methodology established during the UART SV Core, FIFO SV Core, SPI SV Core, and I²C SV Core projects.

---

## 2. Scope

This specification defines the functional and non-functional requirements for Version 1.0 of the APB Peripheral Subsystem.

Version 1.0 includes:

* One synthesizable APB Master
* One APB Interconnect
* One UART APB Peripheral
* One SPI APB Peripheral
* One I²C APB Peripheral
* 32-bit APB address
* 32-bit APB data
* Static address decoding
* Peripheral selection
* APB request routing
* APB response routing
* Unmapped-address error handling
* Single outstanding APB transaction
* Single clock domain
* Memory-mapped peripheral registers
* Reuse of the existing UART, FIFO, SPI, and I²C IP
* UART TX FIFO using Standard FIFO mode
* UART RX FIFO using First-Word Fall-Through (FWFT) mode

The existing UART, FIFO, SPI, and I²C protocol/storage cores shall not be functionally redesigned solely for integration into the subsystem.

The project also includes directed testing, self-checking SystemVerilog testbenches, assertions, synthesis, and static timing analysis as verification and implementation activities.

---

## 3. Features Outside Version 1.0

The following features are outside the scope of Version 1.0:

* Multiple APB Masters
* APB arbitration
* Multiple instances of the same peripheral
* Dynamic address remapping
* APB clock-domain crossing
* AHB-to-APB bridges
* AXI-to-APB bridges
* DMA support
* Interrupt controller
* UART parity
* Configurable UART stop bits
* UART hardware flow control
* UART oversampling enhancements
* Runtime-programmable UART baud rate
* SPI FIFOs
* SPI multi-chip-select control
* I²C multi-master operation
* I²C DMA
* AXI4-Lite interface
* Asynchronous FIFO
* UVM
* Formal verification
* Power-management infrastructure

---

## 4. Functional Requirements

### 4.1 APB Master

The APB Master shall:

* Accept a transaction request from a local command interface.
* Support read and write transactions.
* Generate the APB SETUP phase.
* Generate the APB ACCESS phase.
* Maintain APB transaction control signals until transaction completion.
* Wait for `PREADY`.
* Capture `PRDATA`.
* Capture `PSLVERR`.
* Report transaction completion through the local interface.
* Report transaction error status through the local interface.
* Prevent overlapping transactions.
* Return to the idle state after transaction completion.
* Operate entirely within the subsystem clock domain.

The exact local command/response signal names and handshake timing are defined during the APB Master architecture milestone.

### 4.2 APB Interconnect

The APB Interconnect shall:

* Receive APB transactions from the APB Master.
* Decode the APB address.
* Generate a peripheral select for the addressed peripheral.
* Route APB request signals to the selected peripheral.
* Route the selected peripheral response to the APB Master.
* Ensure that no more than one peripheral is selected for a transaction.
* Generate an error response for unmapped addresses.
* Operate without an independent transaction state machine.

### 4.3 UART APB Peripheral

The UART APB Peripheral shall:

* Provide an APB slave interface.
* Provide memory-mapped control and status registers.
* Accept transmit data through the APB interface.
* Buffer transmit data using a Standard FIFO.
* Supply buffered transmit data to the existing UART transmitter.
* Receive data through the existing UART receiver.
* Buffer received data using an FWFT FIFO.
* Provide received data through the APB interface.
* Expose relevant UART and FIFO status.
* Preserve the existing UART protocol implementation.

### 4.4 SPI APB Peripheral

The SPI APB Peripheral shall:

* Provide an APB slave interface.
* Provide memory-mapped control and status registers.
* Provide transmit data access.
* Provide receive data access.
* Provide SPI transaction control.
* Expose supported SPI configuration controls.
* Interface directly with the existing SPI Master.
* Preserve the existing SPI protocol implementation.

### 4.5 I²C APB Peripheral

The I²C APB Peripheral shall:

* Provide an APB slave interface.
* Provide memory-mapped control and status registers.
* Provide transmit data access.
* Provide receive data access.
* Provide I²C transaction control.
* Provide target slave-address configuration.
* Expose relevant I²C status and error information.
* Interface directly with the existing I²C Master.
* Preserve the existing I²C protocol implementation.

### 4.6 Top-Level Subsystem

The top-level subsystem shall:

* Instantiate the APB Master.
* Instantiate the APB Interconnect.
* Instantiate one UART APB Peripheral.
* Instantiate one SPI APB Peripheral.
* Instantiate one I²C APB Peripheral.
* Connect all modules to the common system clock and reset.
* Provide the external UART, SPI, and I²C interfaces.
* Provide the local command/response interface used to generate APB Master transactions.

---

## 5. Parameters

| Parameter | Description |
|-----------|-------------|
| `ADDR_WIDTH` | Width of the APB address bus. |
| `DATA_WIDTH` | Width of the APB data bus. |
| `UART_DATA_BITS` | Number of UART data bits. |
| `UART_TX_FIFO_DEPTH` | Number of entries in the UART transmit FIFO. |
| `UART_RX_FIFO_DEPTH` | Number of entries in the UART receive FIFO. |
| `UART_BAUD_RATE` | UART baud-rate configuration. |
| `UART_CLOCK_FREQ_HZ` | UART system clock frequency. |
| `SPI_DATA_WIDTH` | Width of each SPI transfer. |
| `SPI_CLOCK_DIV` | SPI Master clock divider. |
| `I2C_DATA_WIDTH` | Width of each I²C data transfer. |
| `I2C_CLOCK_FREQ_HZ` | I²C Master system clock frequency. |
| `I2C_SCL_FREQ_HZ` | Target I²C serial clock frequency. |

The UART TX FIFO shall use Standard FIFO mode.

The UART RX FIFO shall use First-Word Fall-Through (FWFT) mode.

The default UART FIFO depth shall be 16 entries for both transmit and receive paths.

Runtime UART baud-rate programming is outside Version 1.0. The UART baud-rate configuration remains a parameter.

---

## 6. Interface

### 6.1 APB Interface

| Signal | Width | Description |
|--------|------:|-------------|
| `PADDR` | `ADDR_WIDTH` | APB address |
| `PWDATA` | `DATA_WIDTH` | APB write data |
| `PWRITE` | 1 | Read/write selection |
| `PSEL` | 1 | Peripheral select |
| `PENABLE` | 1 | APB access-phase indication |
| `PREADY` | 1 | Indicates transaction completion |
| `PRDATA` | `DATA_WIDTH` | APB read data |
| `PSLVERR` | 1 | Indicates transfer error |

### 6.2 APB Master Local Interface

The APB Master shall provide a local command/response interface containing:

* Transaction-valid indication
* Read/write selection
* Transaction address
* Write data
* Transaction-ready indication
* Transaction-complete indication
* Read data
* Error indication

**To be updated soon.**

### 6.3 Peripheral External Interfaces

The top-level subsystem shall provide the external interfaces required by the integrated peripheral cores:

* UART serial input/output
* SPI serial interface
* I²C open-drain serial interface

Exact top-level signal names shall follow the corresponding integrated core interfaces when top-level integration is implemented.

---

## 7. Address Map

Version 1.0 shall use the following fixed peripheral address windows:

| Peripheral | Base Address | End Address |
|------------|-------------:|------------:|
| UART | `0x0000_0000` | `0x0000_0FFF` |
| SPI | `0x0000_1000` | `0x0000_1FFF` |
| I²C | `0x0000_2000` | `0x0000_2FFF` |
| Reserved / Unmapped | `0x0000_3000` onward | — |

The APB Interconnect shall decode the peripheral window from `PADDR`.

Peripheral-local register decoding shall be performed inside the corresponding APB peripheral wrapper.

---

## 8. APB Transaction Requirements

The APB Master shall generate an APB transaction using the following sequence:

```text
IDLE
  |
  | Transaction accepted
  v
SETUP
  |
  | PSEL = 1
  | PENABLE = 0
  v
ACCESS
  |
  | PSEL = 1
  | PENABLE = 1
  |
  +---- PREADY = 0 --> remain in ACCESS
  |
  +---- PREADY = 1 --> transaction complete
                            |
                            v
                           IDLE
```

During the ACCESS phase:

* `PADDR` shall remain stable.
* `PWRITE` shall remain stable.
* `PWDATA` shall remain stable for write transactions.
* `PSEL` shall remain asserted.
* `PENABLE` shall remain asserted.

Only one APB transaction may be active at a time.

The exact local response timing following APB completion shall be defined as part of the APB Master architecture.

---

## 9. Address Decoding

The APB Interconnect shall perform range-based address decoding.

A transaction shall be routed according to the following mapping:

| Address Range | Selected Peripheral |
|---------------|---------------------|
| `0x0000_0000 - 0x0000_0FFF` | UART |
| `0x0000_1000 - 0x0000_1FFF` | SPI |
| `0x0000_2000 - 0x0000_2FFF` | I²C |
| All other addresses | Unmapped |

No individual register address shall be decoded by the APB Interconnect.

For a mapped transaction, the peripheral-local address shall be derived from `PADDR` relative to the selected peripheral base address.

---

## 10. Request Routing

For a mapped transaction, the APB Interconnect shall:

* Forward `PADDR` to the peripheral interfaces.
* Forward `PWDATA` to the peripheral interfaces.
* Forward `PWRITE` to the peripheral interfaces.
* Forward `PENABLE` to the peripheral interfaces.
* Assert `PSEL` only for the selected peripheral.

Only the selected peripheral shall receive an asserted `PSEL`.

---

## 11. Response Routing

For a mapped transaction, the APB Interconnect shall return the selected peripheral's:

* `PREADY`
* `PRDATA`
* `PSLVERR`

to the APB Master.

Only the response from the selected peripheral shall be visible to the APB Master.

---

## 12. Unmapped Address Behaviour

When `PADDR` does not fall inside any implemented peripheral window:

* No peripheral `PSEL` shall be asserted.
* `PREADY` shall be asserted to terminate the transaction.
* `PRDATA` shall be driven to zero.
* `PSLVERR` shall be asserted.

The unmapped transaction shall therefore complete with an error response without selecting any peripheral.

---

## 13. Peripheral Register Map

### 13.1 UART

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | UART control |
| `0x04` | `STATUS` | UART status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `FIFO_STATUS` | FIFO status |
| `0x14` | Reserved | Reserved |

### 13.2 SPI

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | SPI control |
| `0x04` | `STATUS` | SPI status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `CONFIG` | SPI configuration |

### 13.3 I²C

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | I²C control |
| `0x04` | `STATUS` | I²C status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `SLAVE_ADDR` | Target slave address |

Exact register bit assignments shall be defined during peripheral-wrapper architecture and implementation.

A transaction that attempts to access a register while the associated peripheral resource is unavailable shall be defined during peripheral-wrapper architecture.

**To be updated soon.**

---

## 14. Control and Status Requirements

Control registers shall provide only controls supported by the corresponding existing protocol core.

A control field that initiates a peripheral transaction shall generate a transaction request rather than requiring a persistent level to remain asserted.

Runtime configuration of parameters that are fixed in the existing protocol-core implementation is outside Version 1.0.

The following remain parameterized rather than runtime-programmable unless explicitly supported by the existing core:

* UART baud rate
* SPI clock divider
* I²C timing configuration

Exact control and status bit assignments are architecture-stage details.

**To be updated soon.**

---

## 15. Reset Behaviour

The subsystem shall provide a common clock and reset to the integrated modules.

The existing UART, FIFO, SPI, and I²C cores shall retain their established functional reset behaviour. Any reset adaptation required for subsystem integration shall not functionally redesign the reused cores.

Following reset:

* The APB Master shall return to its idle state.
* APB transaction state shall be cleared.
* The APB Interconnect shall return to its inactive/default response state.
* Peripheral wrapper control state shall return to its reset state.
* The integrated peripheral cores shall follow their respective established reset behaviour.

Exact subsystem-level reset sequencing shall be finalized during integration.

**To be updated soon.**

---

## 16. Parameter Validation

The implementation shall validate configuration parameters during elaboration whenever possible.

The subsystem shall preserve the parameter constraints of the integrated UART, FIFO, SPI, and I²C cores.

Additional subsystem-level parameter constraints shall be defined as required by the implemented parameter set.

**To be updated soon.**

---

## 17. Timing Requirements

* All subsystem sequential logic shall operate on the rising edge of the system clock.
* The subsystem shall operate within a single clock domain.
* The APB Master shall generate one SETUP phase followed by one or more ACCESS cycles until `PREADY` is asserted.
* APB address and control information shall remain stable throughout the ACCESS phase.
* No internally generated clock shall be used by the APB infrastructure.
* Existing protocol-core timing requirements shall remain as defined by the corresponding core specifications.

The initial subsystem timing target for synthesis and static timing analysis shall be 20 ns (50 MHz).

---

## 18. Assumptions

The following assumptions apply to Version 1.0:

* A stable system clock is available.
* All integrated modules operate within the same system clock domain.
* Only one APB Master generates transactions.
* At most one APB transaction is active at a time.
* Peripheral address windows do not overlap.
* Peripheral-local register decoding is performed by the peripheral wrapper.
* Existing protocol/storage cores satisfy their respective interface and reset requirements.
* External peripheral inputs satisfy the synchronization assumptions of the corresponding existing protocol cores.

---

## 19. Verification and Implementation Requirements

Version 1.0 shall be developed and evaluated using:

* Directed testing
* Self-checking SystemVerilog testbenches
* Immediate SystemVerilog assertions
* Waveform analysis
* Generic RTL synthesis using Yosys
* Sky130 HDLL technology mapping
* Static timing analysis using OpenSTA

Verification and implementation results shall be documented after the corresponding milestones are completed.

**To be updated soon.**

---

## 20. Future Enhancements

Future versions of the APB Peripheral Subsystem may include:

* Multiple APB Masters
* APB arbitration
* Interrupt support
* DMA support
* Runtime-programmable UART baud rate
* SPI FIFOs
* AHB-to-APB bridge
* AXI4-Lite-to-APB bridge
* Formal verification
* Additional memory-mapped peripherals