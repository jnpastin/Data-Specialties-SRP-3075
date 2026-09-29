# Decitek 261E7010 Optical Tape Reader

## Overview

This directory contains reverse-engineered schematics and supporting material for the Decitek 261E7010 optical paper tape reader used in the Data Specialties SRP-3075.

The 261E7010 was a third-party OEM reader module incorporated into the SRP-3075. It provides the optical sensing, signal conditioning, and electromechanical transport needed to read eight-channel, one-inch paper tape.

The schematics were recreated through examination of the reader installed in an original SRP-3075. They are not original Decitek or Data Specialties drawings.

## Function within the SRP-3075

The reader forms the input side of the SRP-3075 reader/punch system. It performs three primary functions:

- Detects the eight data channels and sprocket channel
- Conditions the optical sensor outputs into logic-level signals
- Advances the tape one character position when commanded

The reader connects to the [Serial Interface and Reader Control](../SRP3075_Serial-Reader_Control/README.md), which provides reader sequencing, serial conversion, baud-rate generation, and switching of the reader module’s 24V supply.

The reader module does not perform serial conversion or interpret character values.

## Physical Construction

The reader consists of three main functional sections:

- Optical read station
- Signal-conditioning electronics
- Stepper-driven tape transport

Tape passes through a fixed optical read station containing eight data-channel sensors and one sprocket-channel sensor. A single motor drives the feed mechanism and sprocket through a belt.

The reader is designed as a self-contained OEM module with direction and data-polarity capabilities that are not fully exposed by the SRP-3075.

## Optical Read Station

The read station contains nine optical sensing channels:

- Data Channel 1
- Data Channel 2
- Data Channel 3
- Data Channel 4
- Data Channel 5
- Data Channel 6
- Data Channel 7
- Data Channel 8
- Sprocket Channel

The sensors have been identified as photoresistive devices based on observed behavior. Their exact manufacturer and part numbers have not been identified.

Each sensor changes resistance according to the amount of light passing through its corresponding tape position. The resulting signals are processed by the reader’s analog conditioning circuitry.

The sprocket channel identifies the feed hole associated with each character position. It provides the alignment indication needed to determine when the eight data-channel outputs represent valid character data.

## Signal Conditioning

The photoresistor outputs are converted into logic-level signals by circuitry within the reader assembly.

The signal-conditioning circuitry provides:

- Sensor biasing
- Threshold detection
- Logic-level conversion
- Individual outputs for the eight data channels
- A separate sprocket-channel output

The resulting signals are passed to the Serial Interface and Reader Control board for capture and serial transmission.

## Tape Transport

Tape movement is provided by a single variable-reluctance stepper motor.

The motor drives both the tape-feed mechanism and the sprocket through a belt. The transport advances the tape in discrete character-position increments under the control of the reader electronics.

The reader operates in a read-then-step sequence. Character data is observed while the tape is stationary, after which the transport advances the next character position into alignment with the read station.

## Power and Stepping Control

The reader assembly contains the stepper-motor drive circuitry. The Serial Interface and Reader Control board does not switch individual motor phases directly.

The serial board controls reader operation by switching the +24 V power supplied to the reader module. Its reader timing circuitry also provides the request or stepping control used by the module to advance the tape.

The switched supply is identified as `+24V_SW` in the reconstructed schematics.

## Direction Control

The Decitek reader supports bidirectional tape movement.

A direction-control input selects the direction in which the stepper-drive circuitry advances the transport. This reflects the reader’s design as a general-purpose OEM module rather than a subsystem developed specifically for the SRP-3075.

The SRP-3075 does not expose this capability. The direction input has no signal connected on the Serial Interface and Reader Control board, leveraging the internal pull-up which forces the reader to operate in a single direction.

Direction is therefore not selectable by the operator, the host system, or the SRP-3075 control logic.

## Data Polarity

The reader supports selectable output polarity.

A polarity-control input determines the logical interpretation of a detected hole. The module can represent a hole as either:

- Logical one, or mark
- Logical zero, or space

The SRP-3075 does not expose this selection. The polarity input has no signla connected on the Serial Interface and Reader Control board, relying on the internal pull-up, so that a hole is always reported as a logical one, or mark.

Polarity is therefore not configurable by the operator or host system.

## Interface

The reader connects to the Serial Interface and Reader Control board through a 20-position connector.

The interface includes the following categories of connections:

### Power

- Switched +24 V reader supply
- Reader logic and sensing supplies
- Ground connections

### Control Inputs

- Tape-step or reader-request control
- Direction selection
- Hole-polarity selection

### Sensor Outputs

- Reader Channel 1
- Reader Channel 2
- Reader Channel 3
- Reader Channel 4
- Reader Channel 5
- Reader Channel 6
- Reader Channel 7
- Reader Channel 8
- Reader Sprocket

The direction and polarity inputs are fixed by wiring on the Serial Interface and Reader Control board. They are functional capabilities of the reader module but are not variable during normal SRP-3075 operation.

## Repository Contents

This directory contains:

- KiCad project files
- Hierarchical KiCad schematics
- A generated netlist
- A PDF schematic export
- A component-location image

The KiCad schematics and netlist are the editable engineering sources. The PDF is provided for convenient viewing, and the component-location image assists with locating parts on the original assembly.

The repository does not contain a recreated PCB layout. Any placeholder KiCad PCB file should not be interpreted as representing the original board routing.

## Schematic Organization

The reconstructed design is divided into functional sheets covering:

- Optical sensing and signal conditioning
- Tape-reader motor drive
- Reader power supply and distribution

This organization is intended to make the operation of the circuit easier to understand. It does not necessarily reflect the organization of any original Decitek documentation.

## Reverse-Engineering Notes

The schematics were recreated by identifying components, tracing PCB connections, analyzing circuit operation, and comparing the assembly’s behavior with the available Data Specialties documentation.

The examined board includes complete component reference designators, but Decitek used an unconventional scheme for the integrated circuits. Each IC is identified by a single letter rather than a conventional `U`-prefixed number.

KiCad requires the reference to contain a numeric portion, so `_1` is appended to each original IC designation in the reconstructed schematics. Unit letters then identify individual gates or sections within the device. For example, `F_1B` identifies unit B of the IC marked `F` on the original assembly. The suffix does not imply the existence of an `F_2` device.

The reverse engineering of the assembly is considered substantially complete. No programmable logic, firmware, ROM-based control, or other inaccessible logic is present.

## Accuracy and Open Questions

The schematics are believed to accurately represent the examined assembly. Corrections may still be made as restoration and functional testing continue.

Remaining identification questions include:

- Exact photoresistor manufacturer and part number
- Exact specifications of the optical illumination components
- Differences between Decitek production revisions
- Differences between this module and modules installed in other equipment

No significant functional uncertainties are presently known.

## Safety

The reader contains moving mechanical components and motor-drive circuitry. Disconnect power before servicing the assembly or working near the tape transport.

Exercise care when testing the reader outside the complete SRP-3075 because the module depends on externally supplied power and control connections.

## License

This material is licensed under the [terms provided in the repository](../LICENSE).

Manufacturer names, model numbers, and trademarks are used only for identification and historical documentation.