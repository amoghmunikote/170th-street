# NVLink Population (Research)

**NVLink Population — Research Page**

This page documents the theoretical path to enabling NVLink on the CMP 170HX based on direct analysis of the NVIDIA A100 GA100-883 P1001-B02 reference schematics. This is an active area of research with no confirmed successes as of writing, but the schematic analysis has now identified the specific components required with considerably more precision than was previously known.

**What NVLink Would Provide**

NVLink on the A100 provides 600 GB/s of GPU-to-GPU bidirectional bandwidth — 9.4× faster than the current PCIe Gen 1 x4 bandwidth of the CMP 170HX. For multi-GPU compute workloads this would be transformative: it would allow two CMP 170HX cards to act as a single logical GPU with combined HBM2e bandwidth, enabling larger model sizes and dramatically faster inter-GPU communication.

The full A100 NVLink implementation consists of 6 NVHS (NVIDIA High Speed) groups, each carrying 4 lanes of TX and 4 lanes of RX — 24 NVLink lanes total, forming 12 NVLink links at 2 lanes each. This is what the gold fingers on the CMP 170HX PCB are wired to support.

**What Is Present on the PCB**

The NVLink gold finger contacts are physically present on the CMP 170HX PCB along the top edge, in the same location as on the A100 40GB PCIe. This is confirmed by the PCB analysis and the leaked A100 schematics. The gold fingers exist because the CMP 170HX uses the same PCB as the A100, and removing them would have required a custom board revision.

The PCB traces routing from the GA100-105F-A1 die's NVHS signal pads to the gold finger connector pads are present. The physical signal path exists — only the components populating that path are missing.

**What Is Missing — Detailed Component Analysis**

Analysis of the A100 schematics (Pages 4, 5, 9, 16, 17) reveals the following categories of missing components:

**1. Physical NVLink Bridge Connectors**

Three connectors labeled `CON_NVLINK_GA10X` — RIGHT, MIDDLE, and LEFT — form the physical NVLink bridge interface. These are the edge connectors that an NVLink bridge card plugs into. All three are unpopulated on the CMP 170HX. Without these, no NVLink bridge can physically connect.

**2. NVHS Power Decoupling Capacitors**

Page 9 (GPU: NVHS POWER) shows that each NVHS group pair (0,1 / 2,3 / 4,5) requires its own bank of power supply decoupling capacitors on the `PEX_DVDD` rail. All of these are missing on the CMP 170HX:

| Value     | Package | Qty per group | Groups | Total |
| --------- | ------- | ------------- | ------ | ----- |
| 1µF X6S   | 0402 4V | 6             | 3      | 18    |
| 4.7µF X6S | 0402 4V | 3             | 3      | 9     |
| 10µF X6S  | 0603 4V | 3             | 3      | 9     |
| 22µF X6S  | 0603 4V | 2             | 3      | 6     |

**Total decap capacitors required: 42**

**3. Per-PLL Decoupling Capacitors**

Pages 4–5 (GPU: NVHS 0,1,2,3,4,5) show that each NVHS group has a dedicated PLL voltage supply (`NVHS0_PLL_HVDD`, `NVHS1_PLL_HVDD`, `NVHS2_PLL_HVDD`) with its own decoupling network. Each PLL supply uses small capacitor pairs visible in the schematic. Approximately 18 additional capacitors are required across all 6 PLL supplies.

**4. NVHS Termination Resistors**

Each NVHS group has a `NVHS_TERMP` termination resistor network — 6 total, one per NVHS group. These provide the correct impedance termination for the high-speed differential pairs. Without them, signal integrity on the NVLink lanes will be severely compromised regardless of other components.

**5. ID ROM Circuit**

Page 17 (IO: NVHS x16, CON3, MIDDLE connector) shows an ID ROM circuit on the NVLink bridge connector. This is a small I2C EEPROM (with resistors R1024/R1025 visible in the schematic) that stores identification data for the NVLink bridge. The A100 uses this to identify what type of NVLink bridge is attached. This circuit needs to be populated and programmed with appropriate data.

**6. Connector Control Signals**

Each of the three NVLink connectors (RIGHT, MIDDLE, LEFT) has associated control signals that need supporting circuitry:

* `PWR_EN` — power enable for each connector, driven via resistor divider from `3V3_PROT`
* `NVHS_ADDR` — NVLink address assignment per connector
* `RASTERSYNCH` — synchronization signal
* `SWAPREADY` — lane swap readiness signal
* `PEX_RST_BUF*` — reset signal buffered from PCIe reset

Each connector also requires `12V_PWR` and `3V3_PROT` power routing with appropriate filtering.

**The Complete Component List**

Based on schematic analysis, the theoretical complete component list for NVLink population is:

| Category                 | Components                             | Approx Qty | Source                                   |
| ------------------------ | -------------------------------------- | ---------- | ---------------------------------------- |
| NVLink bridge connectors | CON\_NVLINK\_GA10X (edge connector)    | 3          | Salvage from A100 or industrial supplier |
| NVHS decap capacitors    | 1µF/4.7µF/10µF/22µF (various packages) | \~42       | Mouser/DigiKey/LCSC                      |
| PLL decap capacitors     | Small value caps per PLL               | \~18       | Mouser/DigiKey/LCSC                      |
| Termination resistors    | NVHS\_TERMP networks                   | 6          | Per schematic values                     |
| ID ROM                   | Small I2C EEPROM                       | 1          | Standard parts                           |
| Control signal resistors | Pull-ups, dividers per connector       | \~20       | Per schematic values                     |

**The Path to Enabling NVLink — Step by Step**

**Step 1 — Populate power decoupling** The NVHS power decoupling capacitors (42 total) are standard passive components with known values from Page 9. These are the lowest-risk components to start with — they are 0402/0603 SMD parts available from any electronics supplier. Populating these first establishes the power supply foundation before anything else.

**Step 2 — Populate PLL decoupling and termination resistors** Using Pages 4–5 as reference, populate the per-PLL decoupling caps and the NVHS\_TERMP termination resistors for each of the 6 NVHS groups. These are passive components with defined values in the schematic.

**Step 3 — Populate connector control circuitry** Using Pages 16–17 as reference, populate the PWR\_EN resistor dividers, NVHS\_ADDR configuration resistors, and other control signal passive components for each of the three connectors.

**Step 4 — Source and install the NVLink bridge connectors** The `CON_NVLINK_GA10X` physical connectors are the hardest components to source. They are proprietary NVIDIA NVLink bridge connectors not available through standard distributors. The most practical sourcing path is salvage from damaged or end-of-life A100 boards. The connector part number may be identifiable through the A100 BOM or by physically measuring the connector on a working A100.

**Step 5 — Populate and program the ID ROM** The ID ROM circuit needs to be populated and the EEPROM programmed with data that the GPU firmware expects to see when enumerating an NVLink bridge. The correct data values are unknown without a working A100 to read from or access to NVIDIA internal documentation.

**Step 6 — Firmware enablement (the critical unknown)** Even with all hardware populated, it is unknown whether the CMP 170HX firmware will enumerate and enable the NVLink interface. This is the most significant uncertainty. Possibilities are:

* The firmware detects NVLink hardware automatically and enables it (best case)
* The firmware has a capability flag that disables NVLink regardless of hardware presence (requires VBIOS bypass)
* The GA100-105F-A1 die has fuses blown that disable NVLink at the silicon level (worst case, unrecoverable without silicon-level modification)

The third scenario is considered unlikely since the same fuse-blowing mechanism would have been used for CUDA core disabling, and there is no evidence of fuse-based disabling of NVLink specifically. The second scenario is more plausible and is directly linked to the Ampere firmware signing problem documented in the Security & Firmware Research section.

**Current Status**

No publicly documented successful NVLink population on the CMP 170HX exists as of April 2025. The hardware-level work has not been publicly attempted. The schematic analysis on this page represents the most detailed public documentation of what would be required. The firmware question is the largest remaining unknown and cannot be resolved until the hardware work is completed and tested.

**Schematic Reference Pages**

All page references are from the NVIDIA A100 GA100-883 P1001-B02 reference schematic:

| Page | Title                   | Relevance                                                            |
| ---- | ----------------------- | -------------------------------------------------------------------- |
| 4    | GPU: NVHS 0,1,2 and 3   | GPU die NVLink signal pins, PLL supplies, termination                |
| 5    | GPU: NVHS 4 and 5       | GPU die NVLink signal pins continued                                 |
| 9    | GPU: NVHS POWER         | All NVHS power decoupling component values and reference designators |
| 16   | IO: NVHS x16, CON1/CON2 | RIGHT and LEFT NVLink connectors, lane routing, control signals      |
| 17   | IO: NVHS x16, CON3      | MIDDLE NVLink connector, ID ROM circuit, power routing               |

**Resources for Research**

* NVIDIA A100 GA100-883 P1001-B02 reference schematics (see Repair Guide for sourcing)
* NVIDIA A100 NVLink product brief
* NVLink bridge connector physical specifications
* NVIDIA CUDA Multi-Process Service documentation (relevant for multi-GPU NVLink topology)

**Call for Contributors**

This is one of the highest-impact unsolved problems for the CMP 170HX. If you have:

* Experience with high-speed PCB rework at BGA/QFN scale
* Access to a damaged A100 board for connector salvage
* Knowledge of the CMP 170HX or A100 firmware internals
* A working A100 and the ability to read the NVLink ID ROM data

Please document your findings and submit them to the community. A successful NVLink implementation would effectively double the usable compute capacity of a two-card CMP 170HX system and could unlock multi-GPU workloads previously impossible on this hardware.
