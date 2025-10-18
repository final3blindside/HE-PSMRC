# High Endurance - Persistent Space Mission Record Controller (HE-PSMRC)

## Description

The HE-PSMRC is a custom digital peripheral for the Caravel SoC on SKY130, designed specifically for radiation-hardened aerospace applications1. It implements a secure, non-volatile circular fault logging buffer by leveraging the 32 x 32 BM Labs ReRAM NVM IP. This controller guarantees high-endurance record-keeping, eliminating the risk of memory wear-out and ensuring critical diagnostic data persists across power cycles and system anomalies for long-duration space missions. The ReRAM acts as an NVM similar to EEPROM.

## Project Goals

1. Ensure Mission Resilience: Develop a verifiable controller that guarantees persistence of diagnostic data across power loss and radiation events, leveraging the ReRAM's inherent robustness for aerospace applications.
2. Maximize Endurance & Simplicity: Exploit the ReRAM's superior write cycles to eliminate the need for complex software wear leveling, allowing the log to function reliably for the full mission duration.
3. Low-Power Operation: Achieve energy-efficient logging by utilizing ReRAM's low-power write capability, aligning with the power-efficient computing focus of the IP.
4. Successful Integration: Implement the logic and demonstrate seamless communication between the Caravel Management SoC and the NVM IP via the Wishbone bus interface.
   
## Role oF ReRam NVM IP

The ReRAM NVM IP is critical to the HE-PSMRC, serving two essential non-volatile functions that rely on its high endurance and persistence: 1. Fault Log Buffer: The ReRAM crossbar array acts as the primary circular storage for telemetry and fault packets. Its high endurance is necessary to withstand the continuous, high-frequency writes of a real-time system log. 2. Persistent Log Pointer (PLP): A small, dedicated ReRAM area stores the log's next available write address. This pointer must be non-volatile and instantly updated to ensure log integrity even during an immediate power loss, a critical feature for space reliability.


## System Block Diagram 
The HE-PSMRC is instantiated as a custom Wishbone Slave in the Caravel User Project Area, allowing the main processor to manage the logging process.

| Component  | Role | Integration Point |
| :------------- |:-------------:| :-------------:|
| Caravel Management SoC (MCU)     | The main host processor; initiates log write operations by writing fault data to the HE-PSMRC's control registers.    | Wishbone Bus Master |
| Wishbone Bus (WB)      | Standard on-chip interface for communication between the MCU and the custom peripheral.     | Interface |
| HE-PSMRC Controller Logic (FSM)    | The custom RTL (digital logic) that sequences the logging operation (read pointer $\to$ write data $\to$ update pointer $\to$ acknowledge). This block acts as the Wishbone Slave8    | User Project Area |
| ReRAM NVM IP      | The integrated Neuromorphic X1 $32 \times 32$ macro9999. It contains the Crossbar Array (log data) and the dedicated memory for the Persistent Log Pointer (PLP)     | User Project Area |
| Memory Controller / ADC / Decoder      | The essential analog and digital circuitry provided with the ReRAM IP that handles the low-level electrical interface, addressing, and data conversion for the crossbar array.    | Part of ReRAM IP Macro |


## Feasibility on SKY130

The feasibility of the High Endurance Persistent Space Mission Record Controller (HE-PSMRC) on the SkyWater 130nm (SKY130) process is very high $\checkmark$ because the design adheres strictly to the requirements and utilizes available, compatible IP. The project is fundamentally based on integrating the BM Labs ReRAM NVM IP, which is explicitly offered for use with Caravel and is designated for radiation-hardened aerospace applications11. The ReRAM IP functions as an NVM similar to EEPROM 2222and features a standard Wishbone bus interface3333. This alignment with the Caravel platform's communication standard means the digital design effort is focused on creating a well-defined Wishbone Slave controller to manage the memory, a routine task in custom SoC design.

The core challenge of the HE-PSMRC is to build custom digital logic (an FSM) to manage a circular buffer and persistent pointer using the ReRAM IP's capabilities, specifically to ensure data integrity and persistence during power loss. Since the memory is confirmed to have the necessary non-volatile function and the required digital interface is standardized, the project avoids complex analog integration hurdles. Its application directly addresses the contest's focus by leveraging the ReRAM's superior endurance for a critical on-chip data logging function, making it a highly feasible and well-justified candidate


## About the Team

Yuan Yancey E. Labay is a Senior Electronics Engineering student specializing in Microelectronics. He has been a part of award winning satellite design teams and is involved in small satellite systems and design research. He was a member of teams and research groups that have been delegates to the Space Generation Congress 2024 in Milan and the Intenrational Astronautical Congress 2025 in Sydney. He currently serves as Vice-Chairperson of the Students for the Exploration and Development of Space in the Philippines (SEDS Philippines)

He was an intern with Analog Devices Philippines under the Design Verification Team of the Consumer Business Unit.

Most recently, he has participated in the IEEE Solid State Circuit Society's Chipathon 2025.


## Milestones & Timeline

| Date  | Design Phase | Activities and Tasks | Deliverables |
| :------------- |:-------------:| :-------------:| :-------------:|
| Oct 17 - 19 (3 Days)    | 1. Specification & Initial RTL     | Finalize FSM state diagram, register map, and addressing scheme for the Persistent Log Pointer (PLP). RTL Coding: Implement the HE-PSMRC Controller (FSM) and Wishbone Slave interface in Verilog/SystemVerilog. IP Integration: Integrate the BM Labs ReRAM IP black-box model and connection wrappers into the top-level Caravel user project RTL. | Functional RTL & IP Integration Complete $\checkmark$ |
| Oct 20 - 23 (4 Days)      | 2. RTL Verification (Functional & Rigor)     | Verification Package: Develop and run comprehensive RTL testbenches (e.g., using Cocotb). Core Functional Tests: Verify basic Log Write/Read, circular buffer roll-over, and Wishbone transactions. Endurance & Persistence Tests: CRITICAL: Simulate abrupt power/reset assertion to verify the integrity of the Persistent Log Pointer (PLP) and demonstrate high-cycle write endurance. Code Sign-off: Achieve 100% functional coverage on the critical log/pointer logic. | Verified RTL with Comprehensive Verification Package $\checkmark$|
| Oct 24 - 26 (3 Days)      | 3. Synthesis & Static Timing Analysis (STA)     | RTL Synthesis: Use OpenLane to synthesize the verified RTL into a gate-level netlist using the SKY130 standard cell library. Formal Verification: Run LEC (Logic Equivalence Check) to ensure the synthesized netlist is functionally identical to the verified RTL. Initial Timing Review: Run initial STA on the netlist to check for setup/hold violations. | Clean Gate-Level Netlist & SDF Constraints $\checkmark$ |
| Oct 27 - 30 (4 Days)      | 4. Place & Route (P&R) & Physical Verification     | Floorplanning: Define the layout area and correctly place the ReRAM IP Macro within the Caravel boundary. P&R: Use OpenLane to place the standard cells, run Clock Tree Synthesis (CTS), and route the design. Physical Verification (DRC/LVS): Run Design Rule Check (DRC) and Layout Versus Schematic (LVS) to ensure the GDSII adheres to SKY130 rules and matches the netlist. Final STA: Run detailed STA on the extracted layout netlist to confirm timing closure post-routing. | Clean Layout (GDSII) & Final Routed Netlist $\checkmark$ |
| Oct 31 - Nov 1 (2 Days)      | 5. Post-Layout Verification (GDSII Final Check)     | Extract Parasitics: Extract SDF (Standard Delay Format) files from the final layout. Gate-Level Simulation (GLS): Rerun the original Cocotb testbenches using the gate-level netlist and the extracted SDF for accurate timing verification. This is mandatory for tapeout confidence. Documentation (Drafting): Begin finalizing all written materials, design overview, and figure captions. | Fully Verified Post-Layout Netlist & Draft Documentation $\checkmark$ |
| Nov 2 - 3 (2 Days)     | 6. Final Integration & Submission     | gCaravel Integration: Assemble the final GDSII files, integrating the HE-PSMRC core into the complete Caravel harness. Final Pre-Check: Run the ChipFoundry pre-check script to validate the final GDSII against the master template. Final Deliverables: Finalize the project video, screenshots, and complete the AI/LLM log (if applicable). Submission: Submit the GitHub repository URL and all final materials before the 11:59 PM PST deadline on November 3rd. | Tapeout-Ready GDSII Submission 🚀 |
