# Smart Home Electronic Security System

A digital electronics project implementing a keypad-based security system using combinational logic circuits.

## Overview

This project implements a **Smart Home Electronic Security System** designed as part of CS 1105 - Digital Electronics & Computer Architecture. The system uses digital logic components to create a secure access control mechanism for multiple rooms.

**Student**: Eden Gilbert Kisekka  
**Course**: CS 1105 - Digital Electronics & Computer Architecture  
**Instructor**: Joseph Okhuoya  
**Date**: February 11, 2026

---

## Table of Contents

- [System Architecture](#system-architecture)
- [Components](#components)
  - [Priority Encoder](#1-priority-encoder-input-interface)
  - [Multiplexer](#2-multiplexer)
  - [Logic Gates (Comparator)](#3-logic-gates-comparator)
  - [Decoder](#4-decoder)
  - [Demultiplexer](#5-demultiplexer-enhancement)
- [How It Works](#how-it-works)
- [Files](#files)
- [Circuit Implementation](#circuit-implementation)
- [Usage](#usage)
- [Theory](#theory)
- [References](#references)

---

## System Architecture

The security system is composed of four main functional blocks:

```
┌─────────────────┐     ┌──────────────┐     ┌──────────────┐
│  Data Input     │     │   Data       │     │  Processing  │
│  (User Interface)│────▶│  Routing     │────▶│ (Auth)       │
│  - Keypad       │     │  (Selection) │     │  - Comparator│
│  - Encoder      │     │  - MUX       │     └──────┬───────┘
└─────────────────┘     └──────────────┘            │
        │                    │                      ▼
        │                    │               ┌──────────────┐
        │                    │               │   Status     │
        │                    ▼               │  Indication  │
        │               ┌──────────────┐    │  (Feedback)  │
        │               │   Decoder    │    │  - LEDs      │
        │               │  (Visual)    │    │  - Locks     │
        │               └──────────────┘    └──────────────┘
        │
        ▼
┌─────────────────┐
│ Demultiplexer   │
│ (Door Control)  │
└─────────────────┘
```

---

## Components

### 1. Priority Encoder (Input Interface)

**Component**: 10-to-4 Priority Encoder (74LS147)

**Function**: 
- Converts user's decimal button press (0-9) into 4-bit Binary Coded Decimal (BCD) output
- Ensures that if two keys are pressed simultaneously, only the highest value is registered
- Prevents invalid data errors through priority encoding

**Why Priority?**  
A priority encoder ensures system reliability by handling multiple simultaneous key presses gracefully, registering only the highest priority (largest value) input.

### 2. Multiplexer

**Component**: Quad 4-to-1 Multiplexer (MUX)

**Function**:
- The system protects four different rooms (Room 0, Room 1, Room 2, Room 3)
- Each room has a unique, hard-wired 4-bit security code
- User selects the room using two toggle switches (S1, S0)
- The MUX takes the four hard-wired codes as inputs and outputs only the code corresponding to the selected room

**Room Selection Logic**:
| S1 | S0 | Selected Room |
|----|----|---------------|
| 0  | 0  | Room 0        |
| 0  | 1  | Room 1        |
| 1  | 0  | Room 2        |
| 1  | 1  | Room 3        |

### 3. Logic Gates (Comparator)

**Components**: 
- Four 2-input XNOR gates
- One 4-input AND gate

**Function**:
The comparator performs bit-by-bit comparison between the user's input code and the selected room's reference code:

- **XNOR Gates**: Act as "equivalence detectors" - output High (1) only when both inputs are identical
- **4-bit Comparison**: Each XNOR gate compares one bit position
- **AND Gate**: Combines all XNOR outputs - outputs High only if ALL four bits match perfectly

**Truth Table**:
```
User Input:  1010
Reference:   1010
             ↓↓↓↓
XNOR Output: 1111
AND Output:  1 (Access Granted)
```

### 4. Decoder

**Component**: 2-to-4 Line Decoder

**Function**:
- Provides visual feedback to the user
- Takes the same two selection switches used by the Multiplexer
- Activates one of four output lines corresponding to the selected room
- Output lines can be connected to LEDs labeled "Bedroom," "Garage," "Kitchen," "Office," etc.
- Ensures the user knows exactly which door they are attempting to unlock

### 5. Demultiplexer (Enhancement)

**Component**: 1-to-4 Demultiplexer

**Function**:
Routes the "Access Granted" signal to the specific door lock:

- **Data Input**: Single "Access Granted" output from the AND gate
- **Selection Inputs**: Same room selection switches (S1, S0)
- **Operation**: Routes the unlock signal exclusively to the output line corresponding to the selected room
- **Benefit**: Prevents entering the correct code for one room from accidentally unlocking another

---

## How It Works

### Step-by-Step Operation

1. **User Selection**
   - User toggles selection switches (S1, S0) to choose a room
   - Example: Setting switches to `01` selects Room 1

2. **Signal Routing**
   - Switch signals travel simultaneously to:
     - **Decoder**: Activates LED for Room 1, confirming selection
     - **Multiplexer**: Selects "Code 1" (e.g., `1010`) from hard-wired inputs

3. **Data Entry**
   - User enters a code on the keypad (e.g., presses '5')
   - Encoder converts decimal '5' into binary `0101`

4. **Authentication**
   - Logic gates perform comparison:
     ```
     Input:     0101
     Reference: 1010
     XNOR:      0010 (bits don't match)
     AND:       0 (Access Denied)
     ```
   - If user enters correct code (`1010`):
     ```
     Input:     1010
     Reference: 1010
     XNOR:      1111 (all bits match)
     AND:       1 (Access Granted)
     ```

5. **Door Unlock (with Demultiplexer)**
   - When access is granted (AND output = 1):
   - Demultiplexer routes unlock signal to Room 1's solenoid lock only
   - Other rooms remain locked

---

## Files

| File | Description |
|------|-------------|
| `KeyPad.circ` | Logisim-evolution circuit file containing the complete security system implementation |
| `Keypad Design.png` | Circuit diagram screenshot showing the complete system layout |
| `Screenshots/` | Directory containing additional screenshots of the circuit in operation |
| `Keypad Project.lsebdl` | Logisim project file |

---

## Screenshots

### Circuit Overview

![Circuit Overview](Screenshots/Screenshot%202026-02-10%20at%2012.56.44.png)

### Circuit in Operation

![Circuit Operation](Screenshots/Screenshot%202026-02-10%20at%2012.56.53.png)

---

## Circuit Implementation

### Opening the Circuit

1. Download and install [Logisim-evolution](https://github.com/logisim-evolution/)
2. Open `KeyPad.circ` in Logisim-evolution
3. The circuit is organized into functional blocks:
   - Left side: Keypad buttons (0-9) feeding into Priority Encoder
   - Center: Multiplexer with hard-wired room codes
   - Right side: Comparator (XNOR + AND gates)
   - Top/Bottom: Decoder for visual feedback
   - Output: Demultiplexer for door control

### Hard-Wired Security Codes

The system uses 4-bit codes for each room (example values):
- Room 0: `0000` (0)
- Room 1: `0011` (3)
- Room 2: `0101` (5)
- Room 3: `1001` (9)

*Note: These values can be customized in the circuit by modifying the Constant components connected to the Multiplexer.*

---

## Usage

### Simulation Steps

1. **Select Room**
   - Toggle S0 and S1 switches to select desired room
   - Observe which LED lights up on the decoder output

2. **Enter Code**
   - Press the keypad button corresponding to the correct code for the selected room
   - The Priority Encoder converts the button press to BCD

3. **Check Authentication**
   - If code matches: AND gate outputs High (1), access granted
   - If code doesn't match: AND gate outputs Low (0), access denied

4. **Door Control**
   - With correct code: Demultiplexer routes unlock signal to selected room only

---

## Theory

### Boolean Algebra Application

This system demonstrates practical application of Boolean algebra:

**XNOR Operation (Equivalence)**:
```
A XNOR B = (A AND B) OR (NOT A AND NOT B)
Output is 1 when A = B
```

**4-bit Comparison**:
```
Access = (A0 XNOR B0) AND (A1 XNOR B1) AND (A2 XNOR B2) AND (A3 XNOR B3)
```

Where:
- A0-A3: Bits from user's keypad input (via Encoder)
- B0-B3: Bits from selected room's code (via MUX)

### Combinational Logic

The entire system is a combinational circuit where:
- Outputs depend only on current inputs
- No memory elements (flip-flops) in the main logic path
- Immediate response to input changes

### Priority Encoding

The Priority Encoder implements:
```
If multiple inputs active: Output = highest priority input
Priority: 9 > 8 > 7 > 6 > 5 > 4 > 3 > 2 > 1 > 0
```

---

## Technical Specifications

- **Input**: 10-digit keypad (0-9)
- **Output**: 4-bit BCD + Door unlock signal
- **Logic Family**: TTL (Transistor-Transistor Logic)
- **Key ICs**: 74LS147 (Encoder), 74LS153 (MUX), 74LS139 (Decoder)
- **Simulation**: Logisim-evolution v4.0.0

---

## References

Ndjountche, T. (2016). *Digital electronics 1: Combinational logic circuits*. John Wiley & Sons, Incorporated.

---

## Author

**Eden Gilbert Kisekka**  
CS 1105 - Digital Electronics & Computer Architecture  
February 2026

