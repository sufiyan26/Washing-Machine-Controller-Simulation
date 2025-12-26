# Washing Machine Controller – PIC + PICSimLab

This project implements a fully automated washing machine controller on a PIC microcontroller, developed using a **simulation‑first** workflow in PICSimLab/MPLAB X and then validated on hardware. The firmware models a realistic wash cycle with multiple programs, state‑machine‑based control, safety interlocks, and non‑blocking timing using interrupts.

## Core Concept & Objective

- **Goal:** Design a robust, embedded controller that can sequence through **Wash → Rinse → Spin** cycles safely, while allowing the user to select programs and pause/resume without corrupting the cycle.
- **Approach:** Use a deterministic finite‑state machine (FSM) with timer‑driven interrupts for stage timing, debounced push‑button inputs, and clear separation of actuator control and input sensing to ensure stable behavior under all conditions.

## Technical Design

- **Platform:**
  - PIC microcontroller on PICGenios (or equivalent) board.
  - Developed and tested in **PICSimLab** + MPLAB X before running on physical hardware.
- **I/O Mapping:**
  - Inputs: Door switch, Start/Pause button, Program select, Water‑level sensor.
  - Outputs: Motor driver (wash/spin), water valve / pump, buzzer, LEDs, optional 16×2 LCD for UI.
- **Control Logic (FSM):**
  - States such as `IDLE`, `FILL`, `AGITATE`, `DRAIN`, `RINSE`, `SPIN`, `DONE`, and `ERROR`.
  - Each state has clear entry/exit actions (e.g., turn motor on/off, open/close valve).
  - Transitions guarded by conditions like water level reached, timer expiry, or error conditions.
  - Program profile (e.g., “Normal”, “Delicate”) selects different durations for each stage.
- **Timing & Interrupts:**
  - Hardware timers configured to generate periodic interrupts used as the time base.
  - Interrupt Service Routine (ISR) increments stage counters and services input debounce windows.
  - Main loop remains non‑blocking; it reacts to flags set by the ISR and FSM transitions.
- **Safety & Reliability:**
  - Door‑open lockout: no motor activity when door is open; transitions to safe state if opened mid‑cycle.
  - Start/Pause and Emergency Stop handling with software debouncing and edge detection.
  - Brown‑out‑safe initialization and predictable restart into a known safe state (`IDLE`).
  - Optional LCD messages show current stage and remaining time; LEDs mirror machine status.

## Project Structure
├── src/
│ ├── washing_machine.c # Main FSM and control logic
│ ├── timers.c # Timer/ISR configuration
│ └── io_map.h # Pin assignments for inputs/outputs
├── sim/
│ └── picsimlab_config.ini # PICSimLab project setup
└── docs/
└── test_plan.md # Functional and regression test cases


## Example Behavior

- User selects program and presses **Start** → controller transitions from `IDLE` to `FILL`, then automatically proceeds through `AGITATE`, `DRAIN`, `RINSE`, `SPIN`, and `DONE`.
- If the door is opened during `SPIN`, the motor is cut off immediately and the FSM moves to a safe state.
- Pressing **Pause** freezes the current stage; pressing **Start** again resumes timing from where it left off.
- Buzzer and LEDs provide feedback at major phase boundaries and when the cycle completes.

## Skills Demonstrated

Embedded C on PIC • Finite‑state machine design • Timer/interrupt‑based non‑blocking timing • Input debouncing • Safety interlocks and error handling • Simulation‑first development with PICSimLab • Hardware bring‑up and regression testing


