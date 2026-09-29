# Data Specialties SRP-3075

This repository documents the electrical design of a Data Specialties SRP-3075SD paper tape reader/punch through independently recreated KiCad schematics.

The schematics were produced by reverse engineering an original device. They are not original Data Specialties drawings.

## About the SRP-3075

The Data Specialties SRP-3075 is a tabletop, eight-channel paper tape reader and punch system introduced during the 1970s. It combines an optical tape reader and a tape punch in a single enclosure.

The standard SRP-3075 used separate DTL/TTL-compatible parallel interfaces for the reader and punch. The SRP-3075SD added an RS-232 serial interface for both functions and included data buffering to accommodate the difference between the incoming serial data rate and the mechanical speed of the punch.  The "SD" model designation described in the manual was not present on the sample device, it was designated solely as SRP-3075 or ST1 on the badges affixed to the device.

The serial model supports:

- Paper tape punching at 300, 600, or 750 baud
- Paper tape reading at 300, 600, 750, 1200, 1500, 2400, or 3000 baud
- Eight data channels plus the sprocket channel
- Independent reader and punch control
- RS-232 data and handshake signals
- Remote reader and punch control using ASCII DC1 through DC4 characters
- Buffered punch data while the punch mechanism starts or is temporarily unavailable

The punch operates mechanically at up to 75 characters per second. The optical reader operates at up to 300 characters per second.  This aligns with the available serial baud rates.  Since the serial configuration is 8 data bits and 1 stop bit (8-N-1), each character is 10 bits long.

## Repository Purpose

The purpose of this repository is to preserve the electrical design of the SRP-3075SD and provide usable technical information for:

- Restoration and repair
- Circuit analysis and troubleshooting
- Historical preservation
- Replacement circuit development
- Study of late-1970s paper tape equipment

The available manufacturer technical manual describes installation, operation, interfaces, options, and system-level behavior, but does not contain the complete electrical schematics needed to understand or repair the electronics.

## Repository Contents

Each major assembly directory contains some or all of the following:

- KiCad project files
- Hierarchical KiCad schematics
- Generated netlists
- PDF schematic exports
- Component-location images

The KiCad schematics and netlists are the editable engineering sources. PDF exports are provided for convenient viewing, while component-location images assist with identification and comparison against the physical hardware.  DSI did not silkscreen the boards on the sample unit.  Symbol references are the authors, except where noted in the schematics.

The repository does not currently contain recreated PCB layouts. Any placeholder KiCad PCB files should not be interpreted as representations of the original board routing.

## Source Documentation

The primary manufacturer reference used during this project was:

> Data Specialties Inc., *SP75 / SRP3075 "S" Series Technical Manual*, Specification No. 3601A, July 1978.

A scanned copy is available here and through Bitsavers.

The manual documents the product variants, operating procedures, options, interface signals, timing, and system specifications. It does not include the complete board-level electrical schematics recreated in this repository.

## Reverse-Engineering Method

The schematics in this repository were recreated through examination of original hardware. The work included:

- Identifying components and assemblies
- Tracing printed circuit board connections
- Reconstructing connector pinouts
- Identifying power and signal relationships
- Analyzing circuit operation
- Comparing observed behavior with the manufacturer technical manual
- Creating hierarchical KiCad schematics and netlists

The system is implemented primarily with conventional TTL and CMOS logic, timers, comparators, discrete transistor circuits, and electromechanical drivers. It does not depend on programmable logic, firmware, ROM-based control, or undocumented microcode.

Because the behavior is represented directly by the circuitry, the electrical reverse engineering is considered substantially complete. Corrections may still be made as restoration and functional testing of the original device continue.

---

## System Architecture

The SRP-3075 consists of four major electrical assemblies represented in this repository.

### Serial Interface and Reader Control

The serial and reader-control board provides the external RS-232 interface and controls the optical tape reader.

Its major functions include:

- RS-232 transmit, receive, and handshake interfaces
- Serial-to-parallel conversion for received punch data
- Parallel-to-serial conversion for reader data
- Independent reader and punch baud-rate selection
- Baud-clock generation from a 5.0688 MHz crystal
- Recognition of remote-control characters
- Reader request and stepping control
- Reader data capture and transmission
- Tape-status processing
- Switched 24 V power for the reader motor
- Communication with the main logic and punch-control board

See [Serial Interface and Reader Control](./SRP3075_Serial-Reader_Control/README.md)

### Main Logic and Punch Control

The main logic and punch-control board coordinates punch operation and provides the electrical interface to the punch mechanism, front panel, serial board, motors, sensors, and optional parallel interface.

Its major functions include:

- Punch command and readiness logic
- Received-data buffering
- A 128-character punch FIFO
- Punch data registers
- Punch-cycle sequencing
- Punch-solenoid relay drivers
- Sprocket-punch control
- Punch stepper-motor drive
- AC punch-motor control
- Motor speed detection and synchronization
- Feed control
- Front-panel switch and indicator logic
- Tape-low and tape-error handling
- Power-on reset and system-ready generation
- Interconnection with the serial and reader-control board

See [Main Logic and Punch Control](./SRP3075_Main_Logic-Punch_Control/README.md)

### Optical Tape Reader

The reader assembly is a Decitek 261E7010 optical paper tape reader. It detects the eight data channels and sprocket channel and advances the tape using a stepper-driven transport.

The documented reader electronics include:

- Optical sensing for eight data channels
- Optical sprocket-hole sensing
- Signal conditioning for the optical sensors
- Stepper-motor drive circuitry
- Reader motor and transport connections
- Reader power-supply circuitry
- Interface connections to the serial and reader-control board

See [Decitek Optical Tape Reader](./Decitek_261E7010_Tape_Reader/README.md)

### Power Supply

The power-supply assembly provides the voltage rails used by the logic, serial interface, analog circuitry, motors, and electromechanical drivers.

The documented supply includes:

- +5 V logic supply
- +12 V supply
- -12 V supply
- +24 V control and motor supply
- Rectification, filtering, regulation, and protection
- AC input and transformer connections
- Ground and chassis relationships
- Connections to the other system assemblies

See [Power Supply](./SRP3075_PowerSupply/README.md)

## Data Flow

### Punch Path

Serial data enters through the RS-232 interface on the serial and reader-control board. The serial controller converts each received character into parallel data and passes it to the main logic board.

The main logic board stores the received characters in the punch FIFO. This allows data reception to continue while the punch motor starts and accommodates temporary differences between the serial input rate and the mechanical punch rate.

Characters are transferred from the FIFO into the punch registers and presented to the relay-driver channels. The punch sequencer coordinates the data punches, sprocket punch, tape advance, and mechanical synchronization required for each character.

### Reader Path

The reader-control logic requests a tape step and the reader advances the tape by one character position. The optical reader then presents the eight data-channel states and the sprocket indication to the serial board.

The serial controller loads the reader data and transmits the corresponding serial character through the RS-232 interface. Reader operation is controlled by the front-panel switch, remote-control logic, and the RS-232 Request to Send and Clear to Send signals.

## Remote Control

When remote control is enabled, the serial interface recognizes the ASCII device-control characters DC1 through DC4:

| Character | Function |
|---|---|
| DC1 | Start reader |
| DC2 | Start punch |
| DC3 | Stop reader |
| DC4 | Stop punch |

When remote control is disabled, these characters are handled as ordinary received data and may be punched.

---

## Accuracy and Limitations

The schematics are intended to represent the examined hardware as accurately as possible. Signal names have been assigned or normalized where necessary to make the reconstructed design understandable and maintainable.

Although the reverse engineering is substantially complete, errors remain possible. Restoration, measurement, and functional testing may identify:

- Incorrectly traced connections
- Incorrect component values
- Differences between production revisions
- Undocumented factory modifications
- Repairs or modifications made during the life of the examined device

Any such findings will be incorporated into the schematic set as they are verified.

These materials should not be treated as original manufacturer service documentation. Exercise appropriate care when working with the equipment, particularly around AC line voltage, high-current supplies, motors, and electromechanical assemblies.

## Contributions

Corrections and additional information are welcome, particularly:

- Photographs of other SRP-3075 or SP75 units
- Board-revision information
- Component substitutions
- Original service documentation
- Factory schematic packages
- Verified differences between machine configurations
- Restoration and repair findings

Please include supporting observations, measurements, photographs, or documentation when proposing technical corrections.

## License

This repository is licensed under the terms provided in LICENSE.

Manufacturer names, model numbers, and trademarks are used for identification and historical documentation.