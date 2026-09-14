# APB Subsystem Architecture

## 1. Design Overview

The APB Subsystem follows a modular architecture consisting of an APB Master, APB Interconnect, and three memory-mapped APB peripheral wrappers for UART, SPI, and I²C.

The APB Master generates APB read and write transactions from a local command/response interface. The APB Interconnect performs static address decoding, selects the addressed peripheral, and routes APB requests and responses.

The UART, SPI, and I²C wrappers provide memory-mapped control and status interfaces around the existing protocol and storage IP. The existing UART, FIFO, SPI, and I²C cores are reused without functional redesign solely for subsystem integration.

Version 1 provides a single-clock, single-master, single-outstanding-transaction subsystem with fixed peripheral address windows.

---

## 2. Design Philosophy

The APB Subsystem is designed according to the following principles:

- Modular design
- Parameterization
- Reusability
- Synthesizable RTL
- Vendor-independent implementation
- Clear separation between bus infrastructure and peripheral logic
- Reuse of existing IP
- Documentation-driven development

The design keeps APB infrastructure simple and separates:

- Transaction generation in the APB Master
- Address decoding and routing in the APB Interconnect
- Register decoding and peripheral control in the APB wrappers
- Protocol operation in the existing UART, SPI, and I²C cores
- Data buffering in the existing FIFO IP

The implementation avoids vendor-specific primitives wherever possible, allowing the same RTL to target FPGA and ASIC technologies through standard synthesis flows.

---

## 3. Module Hierarchy

```text
                    APB Subsystem
                              │
                              ▼
                         apb_master
                              │
                              ▼
                      apb_interconnect
                    ┌─────────┼─────────┐
                    ▼         ▼         ▼
              UART Wrapper  SPI Wrapper  I²C Wrapper
                    │         │         │
                    ▼         ▼         ▼
                 UART IP     SPI IP      I²C IP
                    │
                    ▼
                 FIFO IP
```

| Module | Description |
|--------|-------------|
| `apb_master` | Generates APB read and write transactions. |
| `apb_interconnect` | Decodes APB addresses and routes requests and responses. |
| `uart_apb_wrapper` | Provides the APB register interface and UART buffering/control logic. |
| `spi_apb_wrapper` | Provides the APB register interface and SPI control/status logic. |
| `i2c_apb_wrapper` | Provides the APB register interface and I²C control/status logic. |
| Top-level subsystem | Integrates the APB infrastructure and peripheral wrappers. |

The UART wrapper contains the UART TX and RX buffering required by Version 1. The TX path uses Standard FIFO mode and the RX path uses FWFT FIFO mode.

The SPI and I²C wrappers interface directly with the existing SPI Master and I²C Master cores rather than reusing their previous top-level verification wrappers.

---

## 4. Data Flow

### APB Request Path

```text
Local Command
     │
     ▼
 APB Master
     │
     ▼
APB Interconnect
     │
     ├───────────────┬───────────────┐
     ▼               ▼               ▼
UART Wrapper     SPI Wrapper     I²C Wrapper
```

The local command interface supplies a transaction request to the APB Master.

The APB Master generates the APB transaction. The APB Interconnect decodes `PADDR` and asserts `PSEL` for the selected peripheral.

### APB Response Path

```text
UART Wrapper ─┐
SPI Wrapper  ─┼──► APB Interconnect ───► APB Master
I²C Wrapper  ─┘
```

For a mapped transaction, the Interconnect returns the selected peripheral's `PREADY`, `PRDATA`, and `PSLVERR` to the APB Master.

### Peripheral Data Flow

#### UART Transmit Path

```text
APB Write
    │
    ▼
UART Wrapper
    │
    ▼
TX FIFO
    │
    ▼
UART Transmitter
    │
    ▼
UART TX Pin
```

Transmit data written through the APB interface is buffered in the UART TX FIFO before being supplied to the existing UART transmitter.

#### UART Receive Path

```text
UART RX Pin
    │
    ▼
UART Receiver
    │
    ▼
RX FIFO
    │
    ▼
UART Wrapper
    │
    ▼
APB Read
```

Received UART data is buffered in the RX FIFO and made available through the memory-mapped interface.

#### SPI Path

```text
APB Write
    │
    ▼
SPI Wrapper
    │
    ▼
SPI Master
    │
    ├──► MOSI / SCLK / CS_n
    │
    ◄── MISO
    │
    ▼
SPI Wrapper
    │
    ▼
APB Read
```

The SPI wrapper converts memory-mapped control and data accesses into operations on the existing SPI Master.

#### I²C Path

```text
APB Write
    │
    ▼
I²C Wrapper
    │
    ▼
I²C Master
    │
    ├────► SDA
    │
    └────► SCL
    │
    ◄──── I²C Bus
    │
    ▼
I²C Wrapper
    │
    ▼
APB Read
```

The I²C wrapper converts memory-mapped control, address, and data accesses into operations on the existing I²C Master.

### Unmapped Access

```text
APB Master
    │
    ▼
APB Interconnect
    │
    ▼
Address Decoder
    │
    ▼
No Match
    │
    ▼
Default Error Response
```

An unmapped address does not select any peripheral. The Interconnect terminates the transaction with the defined error response.

---

## 5. APB Master

The APB Master is responsible for converting a local transaction request into an APB transfer.

### Architectural Decisions

The APB Master uses:

- Single outstanding transaction
- Single clock domain
- Read and write transaction support
- Dedicated SETUP and ACCESS phases
- Registered transaction information
- `PREADY`-based completion
- APB response capture
- No transaction arbitration

The exact local command/response signal names and response timing are defined during the APB Master architecture milestone.

### Datapath Overview

The APB Master datapath consists of:

- Latched transaction address
- Latched write data
- Latched read/write control
- APB transaction state
- Captured read data
- Captured error status
- Local transaction completion status

### State Machine

The APB Master uses the APB transaction sequence:

```text
IDLE
  │
  │ Transaction accepted
  ▼
SETUP
  │
  │ PSEL = 1
  │ PENABLE = 0
  ▼
ACCESS
  │
  │ PSEL = 1
  │ PENABLE = 1
  │
  ├── PREADY = 0 ──► ACCESS
  │
  └── PREADY = 1 ──► transaction complete
                         │
                         ▼
                        IDLE
```

The transaction address and control information remain stable throughout the ACCESS phase.

---

## 6. APB Interconnect

The APB Interconnect provides combinational address decoding and request/response routing between the APB Master and peripheral wrappers.

### Architectural Decisions

The Interconnect uses:

- Static address decoding
- One APB Master
- Three APB peripherals
- One-hot peripheral selection
- Shared APB request signals
- Multiplexed peripheral responses
- Defined default response for unmapped addresses
- No independent transaction state machine

### Address Map

| Peripheral | Base Address | End Address |
|------------|-------------:|------------:|
| UART | `0x0000_0000` | `0x0000_0FFF` |
| SPI | `0x0000_1000` | `0x0000_1FFF` |
| I²C | `0x0000_2000` | `0x0000_2FFF` |

Addresses from `0x0000_3000` onward are reserved/unmapped.

### Request Routing

The Interconnect:

- Receives the APB address and control signals from the Master.
- Decodes the peripheral address window.
- Generates the corresponding `PSEL`.
- Forwards `PADDR`, `PWDATA`, `PWRITE`, and `PENABLE` to the peripheral interfaces.
- Ensures only the selected peripheral receives an asserted `PSEL`.

No individual peripheral register address is decoded by the Interconnect.

### Local Address Generation

Each peripheral wrapper receives a local register address derived from the global APB address relative to its base address.

```text
Global PADDR
     │
     ▼
APB Interconnect
     │
     ▼
Selected Peripheral
     │
     ▼
Local Register Offset
```

### Response Routing

For a mapped transaction, the Interconnect selects the response from the active peripheral:

- `PREADY`
- `PRDATA`
- `PSLVERR`

Only the selected peripheral response is returned to the Master.

### Unmapped Response

For an unmapped address:

```text
PREADY  = 1
PRDATA  = 0
PSLVERR = 1
```

No peripheral is selected for an unmapped transaction.

---

## 7. UART APB Peripheral

The UART APB Peripheral adds the memory-mapped interface and buffering required to integrate the existing UART IP.

The wrapper contains:

- APB register interface
- TX FIFO
- UART transmitter
- UART receiver
- RX FIFO
- FIFO status and control logic

### TX Path

The UART TX FIFO operates in Standard FIFO mode.

```text
APB TX_DATA Write
       │
       ▼
   TX FIFO
       │
       ▼
UART Transmitter
```

The wrapper controls transfer of buffered data from the FIFO into the UART transmitter while preserving the existing UART core behavior.

### RX Path

The UART RX FIFO operates in FWFT mode.

```text
UART Receiver
      │
      ▼
   RX FIFO
      │
      ▼
APB RX_DATA Read
```

The RX FIFO provides buffered received data to the register interface.

The existing UART receiver's data-valid/acknowledgement behavior is used to coordinate reception with RX FIFO availability.

### Register Interface

The UART wrapper provides the following register offsets:

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | UART control |
| `0x04` | `STATUS` | UART status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `FIFO_STATUS` | FIFO status |
| `0x14` | Reserved | Reserved |

Exact bit assignments and access behavior are defined during wrapper implementation.

---

## 8. SPI APB Peripheral

The SPI APB Peripheral provides the memory-mapped interface used to control the existing SPI Master.

The wrapper contains:

- APB register interface
- SPI control logic
- SPI status logic
- Transmit data path
- Receive data path

The wrapper interfaces directly with the existing SPI Master.

### Register Interface

The SPI wrapper provides the following register offsets:

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | SPI control |
| `0x04` | `STATUS` | SPI status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `CONFIG` | SPI configuration |

The existing SPI Master remains responsible for SPI protocol generation.

The SPI clock divider remains parameterized rather than runtime-programmable in Version 1.0.

Exact register bit assignments and control sequencing are defined during wrapper implementation.

---

## 9. I²C APB Peripheral

The I²C APB Peripheral provides the memory-mapped interface used to control the existing I²C Master.

The wrapper contains:

- APB register interface
- I²C control logic
- I²C status and error logic
- Transmit data path
- Receive data path
- Target slave-address configuration

The wrapper interfaces directly with the existing I²C Master.

### Register Interface

The I²C wrapper provides the following register offsets:

| Offset | Register | Description |
|-------:|----------|-------------|
| `0x00` | `CONTROL` | I²C control |
| `0x04` | `STATUS` | I²C status |
| `0x08` | `TX_DATA` | Transmit data |
| `0x0C` | `RX_DATA` | Receive data |
| `0x10` | `SLAVE_ADDR` | Target slave address |

The I²C timing configuration remains parameterized rather than runtime-programmable in Version 1.0.

Exact register bit assignments and control sequencing are defined during wrapper implementation.

---

## 10. Top-Level Integration

The top-level subsystem integrates:

- APB Master
- APB Interconnect
- UART APB Peripheral
- SPI APB Peripheral
- I²C APB Peripheral

All modules operate within the common subsystem clock and reset domain.

The top-level subsystem provides:

- Local command/response access to the APB Master
- External UART interface
- External SPI interface
- External I²C interface

The top-level module is responsible for connectivity and parameter propagation rather than duplicating peripheral protocol logic.

---

## 11. Peripheral Register Organization

Each peripheral wrapper occupies a fixed 4 KiB address window.

```text
0x0000_0000 ───────── UART ───────── 0x0000_0FFF

0x0000_1000 ───────── SPI  ───────── 0x0000_1FFF

0x0000_2000 ───────── I²C  ───────── 0x0000_2FFF

0x0000_3000 ───────── Reserved / Unmapped
```

The Interconnect determines the selected peripheral from the global address.

The selected wrapper determines the register using its local offset.

This separation keeps global address decoding independent from peripheral-specific register organization.

---

## 12. Reset Architecture

The subsystem exposes a common clock and reset to the integrated modules.

The existing UART, FIFO, SPI, and I²C cores retain their established reset behavior rather than being functionally redesigned for subsystem integration.

The APB infrastructure and wrappers are responsible for returning their own control state to the defined reset condition.

Exact reset sequencing and any required reset adaptation are finalized during implementation.

**To be updated soon.**

---

## 13. Future Architecture Extensions

Future versions of the APB Subsystem may include:

- Multiple APB Masters
- APB arbitration
- Interrupt support
- DMA support
- Runtime-programmable UART baud rate
- SPI FIFOs
- AHB-to-APB bridge
- AXI4-Lite-to-APB bridge
- Formal verification
- Additional memory-mapped peripherals
