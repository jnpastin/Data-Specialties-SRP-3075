# SRP-3075 Power Supply

## Overview

This directory contains reverse-engineered schematics and supporting material for the power-supply assembly used in the Data Specialties SRP-3075.

The power supply provides the DC rails required by the logic boards, serial interface, optical tape reader, punch-control circuitry, and electromechanical assemblies.

The schematics were recreated through examination of the power supply in an original SRP-3075. They are not original Data Specialties drawings.

## Function within the SRP-3075

The power-supply assembly converts incoming AC power into the DC supplies used throughout the device.

The documented outputs include:

- +5V for digital logic
- +12V for analog and interface circuitry
- -12V for analog and serial-interface circuitry
- +24V for motors, relays, solenoids, and other electromechanical loads
- Logic ground, identified as `GND`
- 24V power return, identified as `GND_24V`

The supply connects primarily to the [Main Logic & Punch Control](../SRP3075_Main_Logic-Punch_Control/README.md) board, which distributes power to the remaining assemblies.

## Power-Supply Architecture

The reconstructed design is divided into three functional sections:

- +5V rail
- +12V and -12V rails
- +24V rail

The positive regulated supplies use external pass transistors to provide additional output-current capability:

- The +5V rail uses one external pass transistor.
- The +12V rail uses one external pass transistor.
- The +24V rail uses two external pass transistors.
- The -12V rail does not use an external pass-transistor stage.

The schematics preserve the distinction between logic ground and the higher-current 24V return where that distinction is present in the original design.

## +5V Rail

The +5V rail supplies the TTL and CMOS logic used throughout the SRP-3075.

The circuit includes:

- Transformer input
- Rectification
- Bulk filtering
- Voltage regulation
- One external pass transistor
- Current-limiting and protection circuitry
- Output filtering

The regulator controls the external pass transistor, which provides the output-current capability required by the machine's digital logic.

## +12V and -12V Rails

The +12V and -12V rails supply analog, timing, sensing, and RS-232 interface circuitry.

These rails are used by circuitry including:

- RS-232 line interfaces
- Operational amplifiers and comparators
- Analog sensing circuits
- Timing and threshold circuits

The +12V rail uses a regulator with one external pass transistor to provide additional output-current capability.

The -12V rail is implemented separately and does not use an external pass-transistor stage.

## +24V Rail

The +24V rail supplies the higher-power electromechanical portions of the machine.

Loads associated with this rail include:

- Punch relay and solenoid drivers
- Punch stepper circuitry
- Reader module power
- Other 24V electromechanical loads

The +24V regulator controls two external pass transistors to provide the required output-current capability.

The design uses a separate return identified as `GND_24V` for the higher-current 24V load path. The relationship between `GND` and `GND_24V` should be preserved when servicing or reconnecting the assembly.  

## Main Logic Board Connector

The power supply connects to the Main Logic and Punch Control board through a multi-position wiring connector.

During reverse engineering, the connector wiring in the examined device was found not to agree with the corresponding connections on the main logic board. The `GND` and `GND_24V` positions were reversed.

This discrepancy was corrected during restoration by repinning the connector.

The connector shown on the root sheet of the reconstructed schematic represents the corrected configuration, not the as-found wiring. A note on the root sheet documents:

- The original as-found discrepancy
- The corrected `GND` and `GND_24V` positions
- The specialized extraction tool required to remove and reposition the connector contacts

Anyone comparing the schematic against an unrestored device should review this note before altering the connector.

Do not assume that another SRP-3075 has already received the same correction. Verify its wiring against the board connections before applying power.

## Grounding

The schematics distinguish among:

- `GND`, used as the logic and low-power circuit reference
- `GND_24V`, used as the return for higher-current 24V loads
- Chassis or protective earth

These connections serve different functions within the reconstructed design and should not be treated as interchangeable connector positions.

`GND` and `GND_24V` are intentionally routed separately throughout the system and are connected together only at the negative terminal of C3 on the power-supply assembly. This single-point connection limits interaction between high-current, high noise 24V return paths and the logic ground system. The connector correction preserves the intended return-current paths for each ground network.

## Component Reference Designators

The original power-supply assembly was only partially annotated. Approximately half of its components carried Data Specialties reference designators, while the remaining components had no visible references.

Original Data Specialties references are preserved unchanged in the reconstructed schematics.

For components without an original reference, an `X` is inserted into the designator to identify the reference as one assigned during reverse engineering.

Examples:

- `C12` is capacitor 12 as designated by Data Specialties.
- `CX7` is the seventh otherwise-unreferenced capacitor assigned during schematic reconstruction.
- `R19` is an original Data Specialties resistor reference.
- `RX7` is an author-assigned reference for an otherwise-unreferenced resistor.

The original and assigned reference sequences are independent. An `X`-marked reference does not imply that a corresponding original reference exists.

This convention preserves the available manufacturer markings while providing every component with a unique identifier for analysis, documentation, and repair.

## Repository Contents

This directory contains:

- KiCad project files
- Hierarchical KiCad schematics
- A generated netlist
- A PDF schematic export
- A component-location image

The KiCad schematics and netlist are the editable engineering sources. The PDF is provided for convenient viewing, and the component-location image assists with locating parts on the physical assembly.

The repository does not contain a recreated PCB layout. Any placeholder KiCad PCB file should not be interpreted as representing the original board routing.

## Schematic Organization

The reconstructed design is divided into functional sheets covering:

- +5V generation and regulation
- +12V generation and regulation
- +24V generation and regulation
- Transformer, AC connections, output connectors, power distribution, and -12V generation and regulation.

The root sheet provides the overall interconnection among the rail circuits, transformer windings, wiring, and output connectors.

Important restoration notes, including the corrected `GND` and `GND_24V` connector positions, are recorded on the root sheet.

## Reverse-Engineering Notes

The schematics were recreated by:

- Identifying components and their values
- Recording available board reference designators
- Assigning references to unmarked components
- Tracing PCB connections
- Tracing chassis and point-to-point wiring
- Reconstructing connector pinouts
- Analyzing regulation and power-distribution circuits
- Comparing power connections against the other SRP-3075 assemblies

Rail and connector names were assigned or normalized where necessary to make the reconstructed design understandable and maintainable.

The reverse engineering of the assembly is considered substantially complete. Corrections may still be made as restoration, measurement, and functional testing continue.

## Accuracy and Restoration Status

The schematics are believed to accurately represent the restored assembly.

The main logic board connector is intentionally drawn in its corrected configuration. It does not reproduce the reversed `GND` and `GND_24V` wiring found during disassembly.

Possible remaining areas for investigation include:

- Differences between production revisions
- Differences among input-voltage configurations
- Undocumented factory changes
- Prior repairs or field modifications
- Exact identification of components whose markings are incomplete

No significant functional uncertainties are presently known.

## Safety

This assembly contains hazardous AC line voltage, high-current DC supplies, large capacitors, and exposed power components.

Before servicing the power supply:

- Disconnect the device from AC power.
- Allow capacitors to discharge.
- Verify stored voltages with appropriate test equipment.
- Confirm the transformer and line-voltage configuration.
- Verify connector wiring before reconnecting any assembly.
- Observe correct polarity when replacing polarized capacitors.
- Preserve protective-earth and chassis connections.

The +24V supply can deliver sufficient current to damage wiring, connectors, circuit boards, and electromechanical components if incorrectly connected.

The connector-repinning information on the schematic documents a verified restoration correction. It is not a substitute for checking the wiring and board connections in another device.

## License

This material is licensed under the ../LICENSE.

Manufacturer names, model numbers, and trademarks are used only for identification and historical documentation.