# 8-bit Serial Multiplier with Control FSM — RTL to GDSII

This project implements an area-efficient 8-bit serial multiplier, controlled by a dedicated finite state machine, and carries it through the complete digital ASIC design flow — from Verilog RTL all the way to a signed-off physical layout (GDSII). Rather than using a parallel multiplier, the design reuses a single adder across multiple clock cycles to save area, with the FSM sequencing load, shift, and accumulate operations. The full flow covers RTL design and simulation, logic synthesis, formal equivalence checking, place-and-route, and static timing analysis, mirroring the real industry workflow used to tape out digital ASICs.

## Skills & Tools Demonstrated

- RTL design in Verilog (structural and behavioral modeling, FSM design)
- Digital design fundamentals: serial (shift-add) multiplication architecture, datapath/control separation
- Logic synthesis and optimization using **Cadence Genus**
- Physical design (floorplanning, placement, clock tree synthesis, routing) using **Cadence Innovus**
- Static timing analysis and timing sign-off using **Cadence Tempus**
- Logic Equivalence Checking (LEC) between RTL and gate-level netlist
- Gate-Level Simulation (GLS) and design rule checking (DRC)
- Full RTL-to-GDSII ASIC design flow and QoR (Quality of Results) analysis

## What Was Built

- Designed the RTL for an 8-bit serial multiplier (`RTL/serial_multiplier.v`), composed of a multiplicand register, multiplier register with shift logic, adder, 2:1 mux, product register, cycle counter, and a control FSM that sequences the load → check → add → shift states until the multiplication completes.
- Simulated and functionally verified the RTL before synthesis.
- Synthesized the design with Cadence Genus, generating area, timing, power, and clock-gating reports.
- Performed Logic Equivalence Checking (LEC) between the RTL and the synthesized netlist to confirm functional correctness was preserved.
- Carried the synthesized netlist through physical design in Cadence Innovus: floorplanning, placement, clock tree synthesis (CTS), and routing.
- Ran DRC (design rule checking) and connectivity checks on the final layout.
- Performed static timing analysis (setup and hold) and signed off timing using Cadence Tempus.

## Results

- **Timing sign-off: clean.** Post-synthesis QoR reports show 0 total negative slack (TNS) and 0 violating paths across all timing groups (clock, inputs, outputs).
- **Logic Equivalence Check: PASS.** The gate-level netlist was formally proven equivalent to the RTL across all 60 compare points (17 primary outputs + 43 flip-flops), with 0 non-equivalent or aborted points.
- **Physical verification: 0 DRC violations** on the final routed layout.
- **Synthesis results:** 123 leaf cell instances, 446.652 µm² total cell area, and a total power of ~248.5 µW (65.1 nW leakage + 248.4 µW dynamic) at the synthesized clock period.

## Repository Structure

- `RTL/` — Verilog source for the serial multiplier and control FSM.
- `Synthesis_Reports/` — Genus synthesis output: area, timing, power, QoR, and clock-gating reports, plus clock tree synthesis screenshots.
- `Physical_Design/` — Innovus floorplanning, placement, and routing screenshots and commands.
- `Timing_Analysis/` — Setup and hold timing analysis screenshots from Tempus.
- `Verification/` — DRC, connectivity checks, logic equivalence checking (LEC) results, and gate-level simulation output.
- `Documentation/` — High-level architecture diagram of the design.

## Author

Jai B Shah, B.Tech VLSI Design and Technology, VIT Vellore.
