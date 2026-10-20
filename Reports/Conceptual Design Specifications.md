# Conceptual Design
## Automated Combustion Commissioning and Optimization for the Lochinvar Hellcat Combustion System
#### Customer: Lochinvar Corporation
#### Instructor: Christopher Johnson
#### Project Supervisor: Ben Putnam
#### Team Members: Daniel Craig, Charles Lutz, Alex Marquez, Ethan Davenport
#### Course: ECE 4961 Capstone Design 1
#### Date: 10/20/2026

---

## 1. Introduction

The project will develop a prototype that reduces the time and effort required to determine and document appropriate air-damper, gas-damper, and blower settings for the Lochinvar Hellcat combustion assembly. Hellcat already incorporates combustion-control functions, including independent air and gas control and oxygen feedback. The project is not intended to replace the factory burner-management system or develop a new safety controller. It will investigate whether a separate commissioning tool can organize the adjustment process, collect actuator and blower data, evaluate candidate settings, and produce a repeatable record for review by qualified personnel.

A key project constraint is that **the student project will not use gas and will not fire the burner**. Testing will therefore use the supplied physical intake assembly and blower, verified electrical interfaces, actuator-position feedback, tachometer feedback, simulated sensor values, and/or recorded data. The prototype can demonstrate safe command generation, motion control, data logging, and optimization logic without claiming that a setting has been proven safe or optimal under combustion. Any later combustion validation would require a separately authorized activity outside the scope of this gas-free project.

The supplied hardware includes the Lochinvar/A. O. Smith intake assembly, Siemens air and gas actuator references, and the A. O. Smith 8.9-inch blower assembly (part 100144909). The project must verify the actual connector pinouts, signal levels, command methods, travel limits, feedback scaling, and permitted interfaces before energizing or commanding any component. Manufacturer drawings and the Hellcat service documentation take precedence over assumptions based on similar products.

### 1.1 Project goals

The prototype is intended to:

- Make the commissioning workflow more systematic and easier to repeat.
- Control or command the permitted air/gas damper and blower variables through verified interfaces.
- Measure actuator feedback and blower tachometer feedback where those signals are available.
- Accept simulated or recorded combustion measurements, including O₂ and CO, for algorithm development.
- Evaluate candidate settings against configurable constraints rather than optimizing one measurement in isolation.
- Log commands, feedback, test conditions, errors, and results.
- Generate a commissioning-style report that clearly distinguishes simulated data from physical measurements.
- Preserve human review and avoid bypassing or replacing factory safety controls.
- Demonstrate operation without fuel, flame, or live combustion.

## 2. Restatement of the Fully Formulated Problem

Commissioning a combustion system can require evaluating multiple operating points and finding appropriate relationships between air and fuel settings. Manual adjustment can be time-consuming and may produce inconsistent documentation. The project will develop a gas-free prototype that automates the controllable portions of this workflow and demonstrates how an optimization tool could support a qualified technician.

### 2.1 Requirements (“shall” statements)

|    ID&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    | Requirement |
|:--------|:---|
| R-01 | The prototype **shall** provide a repeatable workflow for setting up, running, stopping, and reviewing a commissioning-style test. |
| **R-02** | The prototype **shall** interface with the supplied physical hardware only through electrical interfaces verified against the exact component documentation and/or measurements approved by the supervisor. |
| **R-03** | The prototype **shall** provide independently addressable command channels for the air actuator, gas actuator, and blower if those functions are included in the final hardware configuration. |
| **R-04** | The prototype **shall** acquire and timestamp available actuator feedback, blower tachometer feedback, controller commands, and system status. |
| **R-05** | The prototype **shall** accept simulated or recorded O₂, CO, CO₂, temperature, pressure, and modulation data for algorithm testing when live combustion measurements are unavailable. |
| **R-06** | The optimization software **shall** reject candidate settings that exceed configured command, travel, speed, or data-validity limits. |
| **R-07** | The software **shall** detect missing, stale, implausible, or out-of-range feedback and stop issuing new optimization commands when required feedback is invalid. |
| **R-08** | The prototype **shall** include a readily accessible stop function that halts new commands and places outputs in a defined, documented state. The correct safe state shall be determined from the actuator and electrical-interface documentation; the software shall not assume that removing power is safe for every mechanism. |
| **R-09** | The prototype **shall not** use gas, ignite a burner, or conduct live-combustion testing as part of this project. |
| **R-10** | The prototype **shall not** bypass, disable, or replace a factory burner-management system, flame-proving function, safety interlock, or required lockout. |
| **R-11** | The prototype **shall not** autonomously authorize a real boiler for commercial operation or represent simulated results as validated combustion results. |
| **R-12** | The system **shall** log the date/time, test identifier, software/configuration version, requested settings, measured feedback, simulated/recorded inputs, errors, rejected candidates, and selected result. |
| **R-13** | The system **shall** generate a report containing the tested settings, constraints, data source, optimization result, known limitations, and test status. |
| **R-14** | The design **shall** electrically isolate or appropriately condition signals wherever required by the verified voltage levels, grounding arrangement, noise environment, or component documentation. |
| **R-15** | The team **shall** compare the automated workflow with a documented baseline workflow using measurable criteria such as completion time, command/feedback error, repeatability, and completeness of records. |
| **R-16** | The team **shall** verify hardware behavior first on a bench with fuel isolated and the burner unable to fire, before integrating software-controlled motion. |
| **R-17** | The design **shall** retain a manual approval step before any candidate settings are marked as accepted. |
| **R-18** | The team **shall** document unresolved interface assumptions and shall not energize a component until its supply requirements, connector pinout, and command/feedback signals are confirmed. |
### 2.2 Origins of constraints

| Constraint                                                   | Origin and design effect                                                                                                                     |
|:-------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| No gas or live firing                                        | Project scope and laboratory safety constraint. All tests use dry bench operation, simulated inputs, or recorded data.                       |
| Factory safety system remains authoritative                  | Manufacturer requirements, public safety, and professional engineering responsibility. The prototype is not a burner-management replacement. |
| Use the supplied Hellcat intake assembly and 8.9-inch blower | Customer-provided resources and project scope. Mechanical/electrical compatibility must be confirmed.                                        |
| Exact actuator interface not yet verified                    | Supplied component drawings and connector details must be reconciled before controller selection is finalized.                               |
| No source code for the existing controller                   | Sponsor constraint. The team will develop an independent application and use only documented, permitted interfaces.                          |
| Safe command limits and feedback validation                  | Electrical and mechanical safety; prevents unintended travel, overspeed, and invalid optimization results.                                   |
| Manufacturer limits govern all commissioning assumptions     | Customer and product documentation. No limits will be invented from a spreadsheet or inferred from another actuator model.                   |
| Human review of results                                      | Ethical and professional responsibility. The tool supports commissioning; it does not certify the boiler.                                    |
| Student budget and schedule                                  | Course resource constraints. Existing lab instruments and sponsor-provided components will be used where available.                          |

## 3. Comparative Analysis of Potential Solutions

The project considered five conceptual architectures. The comparison focuses on gas-free prototyping, the ability to interface with multiple components, development effort, cost, and future extensibility.

| Option                                                        | Description                                                                                                                    | Advantages                                                                                               | Disadvantages / risks                                                                                                                       | Assessment                                                                                                                    |
|:--------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------|
| A. Manual-only test and spreadsheet                           | Technician moves hardware manually and records readings in a spreadsheet.                                                      | Lowest cost; minimal interface electronics; useful as a baseline.                                        | Does not demonstrate automated motion, interlocks in software, or repeatable data acquisition.                                              | Retain as a comparison baseline, not the final prototype.                                                                     |
| B. Microcontroller with isolated I/O                          | MCU-based controller, signal conditioning, position/tach inputs, and a separate user-interface computer.                       | Deterministic I/O; relatively low cost; good for pulse timing and command generation.                    | More custom firmware and interface design; more work to build a convenient report/UI workflow.                                              | Feasible alternative if interface timing or electrical noise requires it.                                                     |
| C. Single-board computer plus dedicated interface electronics | A Raspberry Pi-class computer runs the UI, logging, and optimization; isolated driver/input hardware handles physical signals. | Strong Python/data-analysis support; low-cost UI and report generation; easy to develop and demonstrate. | Linux is not a hard real-time controller; GPIO is not industrial I/O; all physical signals need suitable interface hardware and validation. | **Preferred conceptual architecture**, with time-critical pulse generation delegated to suitable hardware if necessary.       |
| D. Industrial PLC/controller                                  | PLC with industrial I/O and HMI.                                                                                               | Rugged I/O ecosystem; predictable control behavior; familiar industrial architecture.                    | Higher cost; programming/licensing overhead; may be excessive for a gas-free student prototype.                                             | Preferred only if the sponsor requires industrial hardware or the final interface cannot be implemented safely with Option C. |
| E. Direct modification of factory burner controller           | Modify factory firmware or insert commands into safety-critical logic.                                                         | Potentially direct access to internal signals.                                                           | Source code unavailable; high safety/certification risk; risks compromising factory protections.                                            | **Rejected.** The project will not modify or bypass factory safety logic.                                                     |

### 3.1 Selected solution

Option C is selected as the initial architecture: a single-board computer for the operator interface, data logging, reporting, and optimization, combined with dedicated, electrically appropriate interface hardware for actuator and blower signals. This selection keeps the software accessible while acknowledging that raw computer GPIO is not a suitable direct interface to unknown industrial signals.

The controller selection remains conditional on verification of the actual hardware interfaces. The team will first identify the exact electrical behavior of the VA33.11A-U3220G9 air actuator, VA33.VKG-28G gas actuator, and 100144909 blower. If the actuator command requires timing accuracy or electrical characteristics that the selected computer cannot reliably provide, a microcontroller or industrial I/O module will generate the low-level signals while the computer remains the supervisory application.

Previous discussions have identified the Siemens LMV37.400Ax/LMV37.420Ax and OCI412.10 interface as possible communication options for future evaluation. They are not confirmed as the selected solution: compatibility with the supplied VA33 actuators, access to the required commissioning parameters, and suitability for the gas-free test scope must be verified from the applicable documentation. The factory controller must remain responsible for its intended control and safety functions; the student prototype will not bypass or replace those functions. The team will not assume that an LMV37, OCI412, SQM33, or other controller/interface is compatible simply because it is associated with Siemens combustion equipment.

## 4. High-Level Solution

The prototype is divided into six subsystems:

1.  Operator interface and workflow.
2.  Supervisory computer and optimization software.
3.  Actuator command and feedback interface.
4.  Blower command and tachometer interface.
5.  Sensor/data-input and simulation subsystem.
6.  Logging, reporting, and test validation.

The prototype will have two operating modes:

- **Bench hardware mode:** commands and reads only verified physical interfaces, with fuel isolated and no possibility of burner firing under the approved test setup.
- **Simulation/replay mode:** uses generated or recorded measurement data to test optimization logic, safety bounds, report generation, and error handling without relying on a combustion event.

The software will separate command generation from measurement evaluation. Candidate settings will be generated inside a bounded search space, checked against configuration limits, sent through the interface, and then evaluated only after the system reports a stable and valid state. Simulated O₂/CO data will be explicitly labeled as simulated or replayed. Since the project does not use gas, the prototype can validate software behavior and electromechanical response but cannot validate actual combustion quality, emissions, or burner performance.

### 4.1 Hardware block diagram

``` mermaid
flowchart TB
    OP[Operator / Technician]
    UI[Operator Interface<br/>Setup, status, stop, review]
    PC[Supervisory Computer<br/>Workflow, optimization, limits]
    LOG[Data Logger and Report Generator]
    SIM[Simulation / Recorded Data Input<br/>O2, CO, temperature, pressure, load]
    IF[Isolated I/O and Signal Conditioning]
    AIR[Air Damper Actuator<br/>Command and position feedback]
    GAS[Gas Damper Actuator<br/>Command and position feedback]
    BLOW[Blower Assembly<br/>PWM command and tach feedback]
    PWR[Protected Low-Voltage Power<br/>Fusing, regulation, isolation as required]
    SAFE[Physical Test Safeguards<br/>Fuel isolated; burner cannot fire]

    OP <--> UI
    UI <--> PC
    PC <--> LOG
    SIM --> PC
    PC <--> IF
    IF <--> AIR
    IF <--> GAS
    IF <--> BLOW
    PWR --> IF
    PWR --> AIR
    PWR --> GAS
    PWR --> BLOW
    SAFE -. "test condition verified before energizing" .-> IF
```

**Block and connection definitions**

| Block / connection           | Function                                                                                               | Interface and constraints                                                                                                          |
|:-----------------------------|:-------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------|
| Operator interface           | Allows configuration, starting a permitted test, stopping, viewing status, and approving a result.     | Local UI; the stop action must remain accessible during a run.                                                                     |
| Supervisory computer         | Executes state machine, bounded optimization, validation, and orchestration.                           | Internal software interfaces; it must not be the only safety measure for physical hazards.                                         |
| I/O and signal conditioning  | Converts software requests to verified electrical commands and converts feedback to safe logic levels. | Exact voltage, current, isolation, and timing requirements are pending component verification.                                     |
| Air actuator                 | Moves the air damper to a commanded position when supported by its actual interface.                   | Command method, travel, feedback, and permissible limits must be taken from the exact actuator documentation.                      |
| Gas actuator                 | Provides physical gas-damper motion during dry testing only. No gas is connected or used.              | Physical movement is allowed only under the approved no-gas test setup; do not infer valve position from command percentage alone. |
| Blower                       | Responds to a verified speed command and provides tachometer feedback if supported.                    | AC line power remains isolated from low-voltage control electronics; PWM and tach signal levels must be confirmed.                 |
| Simulation/replay input      | Supplies O₂, CO, CO₂, temperature, pressure, and firing-rate data to exercise software logic.          | Every value carries a source label and timestamp. Simulated data cannot be presented as live measurements.                         |
| Data logger/report generator | Stores time-series records and creates a summary of the run.                                           | Logs include configuration/software version, units, source of measurements, faults, and rejected candidates.                       |
| Protected low-voltage power  | Supplies interface electronics and actuators within their rated limits.                                | Power rails, grounding, fuse sizes, and wire gauges are selected from verified datasheets and load measurements.                   |
| Physical test safeguards     | Ensures the test is conducted with gas isolated and burner firing prevented.                           | A documented pre-test checklist and supervisor approval are required. This is not a software-only safeguard.                       |

### 4.2 Operational flowchart

``` mermaid
flowchart TD
    A([Power on]) --> B[Load configuration and hardware profile]
    B --> C{Fuel isolated and burner unable to fire?}
    C -- No --> X[Do not start; report failed precondition]
    C -- Yes --> D[Verify interface, feedback, limits, and stop function]
    D --> E{Self-check passed?}
    E -- No --> X
    E -- Yes --> F[Operator selects bench or simulation/replay mode]
    F --> G[Select test profile and allowed command bounds]
    G --> H[Record baseline readings and configuration]
    H --> I[Generate next candidate setting]
    I --> J{Candidate within all limits?}
    J -- No --> K[Reject candidate and log reason]
    K --> N{Stop requested or search complete?}
    J -- Yes --> L[Apply command through verified interface]
    L --> M[Wait for feedback/stability; acquire data]
    M --> O{Feedback valid and system stable?}
    O -- No --> P[Stop new commands; log fault]
    O -- Yes --> Q[Evaluate objective and constraints]
    Q --> R[Log command, feedback, and result]
    R --> N
    N -- No --> I
    N -- Yes --> S[Select best valid candidate or report no valid result]
    S --> T[Operator reviews and approves report]
    T --> U([End test])
    P --> V[Return to documented safe state and require operator review]
    X --> U
```

### 4.3 Operating states

| State          | Behavior                                                                                              | Exit condition                                |
|:---------------|:------------------------------------------------------------------------------------------------------|:----------------------------------------------|
| Initialization | Load hardware profile, configuration, software version, and command bounds.                           | Configuration valid.                          |
| Pre-check      | Confirm gas isolation, burner-disabled test setup, feedback availability, and stop function.          | All required checks pass.                     |
| Ready          | Display test mode and allow operator to select a test.                                                | Operator starts or cancels.                   |
| Command        | Apply one bounded command through the verified interface.                                             | Command completed or timeout occurs.          |
| Stabilization  | Wait for physical feedback to settle or for a configured test interval.                               | Stability criteria met or timeout occurs.     |
| Measurement    | Capture physical feedback and simulated/replayed sensor values.                                       | Data passes validity checks.                  |
| Evaluation     | Calculate objective score and enforce hard constraints.                                               | Candidate accepted or rejected.               |
| Stop/Fault     | Stop issuing new commands; transition outputs to the documented safe state; preserve diagnostic logs. | Operator inspects and resets the system.      |
| Review         | Show best valid candidate, test data source, warnings, and report.                                    | Operator approves, exports, or closes report. |

## 5. Atomic Subsystem Specifications

All numerical electrical values and command limits must be obtained from the exact manufacturer documentation or verified measurements before hardware commissioning. The specifications below define required behavior; they do not assume unverified actuator pinouts or calibration values.

### 5.1 Subsystem A — Operator interface and workflow

**Function:** Provide a clear operator workflow for selecting a mode, confirming prerequisites, starting/stopping a test, monitoring status, and reviewing results.

**Inputs**

- Operator commands and configuration values.
- System state, feedback, warnings, and faults from the supervisory application.
- Test profile and permitted operating bounds.

**Outputs**

- Start/stop requests.
- Approved test configuration.
- Status display and alerts.
- Report-review and export requests.

**Interfaces**

- Internal software API between UI and supervisory application.
- Local display and keyboard/mouse or touchscreen.
- No direct connection to actuator power or safety-critical hardware.

**Specifications**

- **UI-01:** The interface shall display the selected mode as `BENCH HARDWARE` or `SIMULATION/REPLAY`.
- **UI-02:** The interface shall require confirmation of the gas-isolated, burner-disabled test preconditions before each physical run.
- **UI-03:** The interface shall provide a persistent stop control during a run.
- **UI-04:** The interface shall display commanded and measured actuator positions and blower speed where available, with units and signal-source labels.
- **UI-05:** The interface shall display stale or invalid data as invalid rather than retaining it as if current.
- **UI-06:** The interface shall identify every simulated or replayed sensor value.
- **UI-07:** The interface shall require operator review before a candidate is marked accepted.
- **UI-08:** The interface shall show fault cause, timestamp, and required recovery action when known.
- **UI-09:** The interface shall prevent the user from configuring limits outside the verified hardware envelope without an explicit engineering configuration change.
- **UI-10:** The interface shall support exporting a report and raw data in a documented format such as CSV and PDF/HTML.

**Major functional sequence:** Load profile → show preconditions → run self-check → allow start → display live status → stop or finish → review warnings/results → export report.

### 5.2 Subsystem B — Supervisory computer and optimization software

**Function:** Coordinate the test, enforce command bounds, select candidate settings, evaluate data, and decide whether to continue or stop.

**Inputs**

- Operator configuration and run commands.
- Command/feedback data from the I/O subsystem.
- Simulated or recorded combustion measurements.
- Configured limits and objective-function weights.

**Outputs**

- Bounded actuator/blower commands.
- Requests for measurements and status.
- Candidate scores and accept/reject decisions.
- Fault and stop requests.
- Data records for logging.

**Interfaces**

- Internal software modules with versioned configuration.
- Serial, USB, Ethernet, or GPIO-connected interface hardware only as verified by the selected interface module.
- Any communication protocol used to a device must be confirmed from its datasheet; no protocol is assumed merely from a connector type.

**Specifications**

- **SW-01:** The application shall implement an explicit state machine with initialization, pre-check, ready, command, stabilization, measurement, evaluation, stop/fault, and review states.
- **SW-02:** The application shall validate all configuration values, units, bounds, and required fields before a run.
- **SW-03:** The optimization routine shall search only within a finite, configured set of allowed candidate values.
- **SW-04:** The routine shall apply hard safety and validity constraints before comparing objective scores.
- **SW-05:** The routine shall not select a candidate when required measurements are missing, stale, implausible, or outside their configured valid range.
- **SW-06:** The application shall use a configurable stabilization criterion and timeout. Values shall be set from bench measurements and supervisor review rather than guessed.
- **SW-07:** On a stop request, communication timeout, or invalid critical feedback, the application shall stop issuing new optimization commands and invoke the documented stop behavior.
- **SW-08:** The application shall not autonomously change safety-critical factory controller settings.
- **SW-09:** The application shall separate measured physical values, simulated values, and replayed values in storage and reports.
- **SW-10:** The application shall support deterministic replay of a saved test dataset so that algorithm changes can be compared against identical inputs.
- **SW-11:** The application shall log the objective-function version, configured constraints, software version, and candidate sequence.
- **SW-12:** The application shall be tested with unit tests for limit enforcement, timeouts, missing data, invalid sensor values, and stop behavior before physical motion tests.

**Optimization concept:** The algorithm will evaluate air-damper command, gas-damper command, and blower command as independent candidate variables only where the physical hardware and test plan permit. An objective function may score deviation from target values, repeatability, and operating smoothness. O₂/CO data used in the first phase will be simulated or replayed. Hard limits override any score: a candidate outside configured bounds or with invalid inputs is rejected regardless of its score.

### 5.3 Subsystem C — Actuator command and feedback interface

**Function:** Provide verified, independently addressable command and feedback paths for the supplied air and gas damper actuators.

**Inputs**

- Requested command from the supervisory computer.
- Actuator position/status feedback.
- Low-voltage supply and any required enable signals.

**Outputs**

- Electrical command signals appropriate to each exact actuator.
- Scaled and conditioned feedback to the supervisory computer.
- Interface fault/status signals where supported.

**Interfaces**

- Electrical command and feedback wiring from the verified Siemens actuator connectors.
- Digital pulse, direction, analog, or other interface only if explicitly supported by the exact part documentation.
- Isolated digital/analog inputs or a dedicated driver board as required by verified voltage and current ratings.

**Specifications**

- **ACT-01:** The team shall identify the exact pinout, supply voltage, maximum current, command method, travel limits, and feedback method for each supplied actuator before energizing it.
- **ACT-02:** The interface shall support separate commands for air and gas actuators if both are included in the selected test configuration.
- **ACT-03:** The interface shall condition signals to prevent a component’s voltage levels from exceeding the controller’s electrical ratings.
- **ACT-04:** The system shall enforce software travel/command limits based on verified mechanical endpoints and manufacturer documentation.
- **ACT-05:** The system shall compare requested movement with available feedback and flag a mismatch that exceeds a configured tolerance or timeout.
- **ACT-06:** The system shall not infer actual valve position solely from the commanded percentage.
- **ACT-07:** The system shall prevent commands when feedback is invalid or the physical test preconditions are not satisfied.
- **ACT-08:** The stop response and unpowered behavior shall be documented for each actuator before the first powered test.
- **ACT-09:** Position feedback shall be calibrated against verified reference points where the actuator provides suitable feedback.
- **ACT-10:** The team shall verify motion direction, travel range, repeatability, and endpoint behavior using a gas-isolated bench setup.

**Unknowns to resolve:** Exact connector pinouts and electrical ratings; whether position feedback is directly available; command resolution and travel mapping; safe behavior on power loss; whether separate driver electronics are required. These are critical design inputs, not values to be guessed.

### 5.4 Subsystem D — Blower command and tachometer interface

**Function:** Command the supplied 8.9-inch blower assembly and measure its speed where supported by the actual blower interface.

**Inputs**

- Requested blower command.
- Tachometer pulse signal or other speed feedback.
- Blower power and control supply.

**Outputs**

- Verified blower command signal.
- Calculated speed and feedback-validity status.
- Timeout or command/feedback mismatch indication.

**Interfaces**

- The sponsor-provided A. O. Smith blower assembly, part 100144909.
- Low-voltage control connector for PWM/tachometer signals if confirmed on the exact revision.
- Separate 120 VAC blower power path, physically and electrically separated from low-voltage control electronics.

**Specifications**

- **BL-01:** The team shall verify the exact blower model/revision, connector pinout, line-power requirements, control-signal voltage, PWM frequency/duty requirements, tachometer signal levels, and pulses per revolution.
- **BL-02:** The low-voltage controller shall not directly switch the blower’s AC line input unless a specifically rated and approved switching interface is designed for that purpose.
- **BL-03:** The interface shall use appropriate isolation, protection, and signal conditioning based on verified documentation.
- **BL-04:** The software shall convert tachometer pulses to speed only after the pulses-per-revolution scaling is verified.
- **BL-05:** The software shall detect loss of tachometer feedback and flag it instead of treating missing pulses as a valid speed measurement.
- **BL-06:** The team shall test command-to-speed response and repeatability under a gas-free bench setup.
- **BL-07:** The system shall enforce verified speed-command bounds and shall stop new optimization commands when feedback is invalid.
- **BL-08:** Any blower operating limits used in software shall come from the blower documentation and supervisor-approved test plan.

### 5.5 Subsystem E — Sensor, simulation, and data-input subsystem

**Function:** Supply physical actuator/blower feedback and simulated or recorded combustion variables to the supervisory application.

**Inputs**

- Physical position and tachometer feedback.
- Recorded datasets.
- Simulation configuration and operator-selected test profile.
- Optional measurements from approved laboratory instruments.

**Outputs**

- Timestamped, unit-labeled data values.
- Validity flags and source labels.
- Sensor plausibility warnings.

**Interfaces**

- Conditioned digital/analog input or documented communications protocol for physical feedback.
- CSV or another documented format for replayed datasets.
- Internal software interface for simulated O₂, CO, CO₂, temperature, pressure, and modulation values.

**Specifications**

- **DA-01:** Each data field shall include a name, unit, timestamp, and source identifier (`physical`, `simulated`, or `replayed`).
- **DA-02:** The system shall validate data type, range, timestamp, and freshness before using a value in optimization.
- **DA-03:** The system shall support replaying the same dataset repeatedly for regression testing.
- **DA-04:** Simulated combustion data shall be clearly marked in the user interface, stored record, and report.
- **DA-05:** The software shall not claim that simulated O₂ or CO values demonstrate actual combustion performance.
- **DA-06:** If a physical sensor is added, its range, accuracy, calibration interval, response time, output format, and interface shall be recorded before integration.
- **DA-07:** Missing or invalid critical data shall cause candidate rejection and may cause the run to stop according to the approved test configuration.
- **DA-08:** Time alignment between commands and measurements shall be sufficient to identify which feedback belongs to each candidate; the exact sampling rate shall be established during bench characterization.

### 5.6 Subsystem F — Data logging, reporting, and validation

**Function:** Preserve an auditable record of test configuration, commands, feedback, optimization decisions, and test outcomes.

**Inputs**

- Events and measurements from all software subsystems.
- Operator and test identifiers.
- Software version, configuration version, and source-data metadata.

**Outputs**

- Time-series log.
- Summary report.
- CSV export for analysis.
- Validation metrics and comparison plots/tables.

**Interfaces**

- Internal logging API.
- Local storage and removable/exported files.
- CSV for raw records and PDF/HTML or Markdown for human-readable reports.

**Specifications**

- **LOG-01:** Every run shall receive a unique test identifier.
- **LOG-02:** The log shall contain timestamps, commands, available feedback, data-source labels, candidate scores, accept/reject decisions, and faults.
- **LOG-03:** The report shall list the configured limits and the source used to establish them.
- **LOG-04:** The report shall state whether data were physical, simulated, or replayed.
- **LOG-05:** The report shall include a limitations section and shall not label a gas-free test as a successful combustion commissioning.
- **LOG-06:** Raw data shall be retained in a format that can be independently analyzed.
- **LOG-07:** The team shall compare manual-baseline and automated workflow time, command/feedback tracking error, repeated-run variation, data completeness, and fault-response behavior.
- **LOG-08:** Acceptance thresholds shall be agreed with the project supervisor before final testing and documented in the test plan.
- **LOG-09:** The logger shall preserve fault records when a run stops unexpectedly.
- **LOG-10:** The software shall not silently overwrite prior test results.

## 6. Ethical, Professional, Safety, and Standards Considerations

### 6.1 Public safety and professional responsibility

Combustion systems can create fire, explosion, high-temperature, and carbon-monoxide hazards. Because the project will not use gas or fire the burner, the design and test plan are constrained to dry bench operation and simulation/replay. The gas-free constraint is a project boundary, not proof that every electrical or mechanical activity is automatically safe. Actuators and the blower can still cause pinch, rotating-equipment, electrical, and stored-energy hazards.

The team shall:

- Keep fuel disconnected/isolated throughout project testing and verify the burner cannot fire.
- Obtain supervisor approval for wiring, energization, and mechanical-motion tests.
- Use manufacturer documentation to establish electrical and mechanical limits.
- Keep line-voltage blower wiring separated from low-voltage signal wiring.
- Use appropriate fusing, enclosures, strain relief, grounding, and signal isolation.
- Provide a stop function and document the physical output behavior on stop, timeout, and power loss.
- Never bypass the factory controller’s safety functions or represent the student prototype as a safety-rated control.
- Clearly label all simulated data and all reports as gas-free prototype results.
- Document unresolved risks, failed tests, and limitations honestly.

### 6.2 Standards and guidance

The team shall review the current editions and applicability of relevant requirements with the customer and project supervisor. Listing a standard does not by itself establish that the student prototype is certified or compliant.

| Standard / guidance                                                                   | Relevance to design                                                                                                                                                            |
|:--------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lochinvar Hellcat service and installation documentation                              | Primary source for permitted commissioning practices, operating limits, hardware configuration, and factory safety requirements.                                               |
| NFPA 54, National Fuel Gas Code                                                       | Relevant background for fuel-gas system safety; the project itself will not connect or use gas. Applicable requirements for the host system must be confirmed by the customer. |
| CSA/ANSI Z21.13 / CSA 4.9, gas-fired low-pressure steam and hot-water boilers         | Relevant to the host boiler’s product-safety context; not a claim of certification for the prototype. Confirm the edition applicable to the actual equipment.                  |
| NFPA 70, National Electrical Code                                                     | Relevant to electrical installation practices where applicable, including wiring and protection. The laboratory and supervisor determine applicable requirements.              |
| IEC 60204-1, Safety of machinery — Electrical equipment of machines                   | Useful guidance for electrical design, stop behavior, wiring, and machine-related hazards where applicable.                                                                    |
| ASHRAE Handbook, Fundamentals — combustion and fuels; Systems and Equipment — boilers | Technical background for combustion, excess air, and boiler performance. Simulated data cannot establish actual combustion performance.                                        |
| ASHRAE Standard 202, The Commissioning Process                                        | Useful process guidance for documentation, verification, issue tracking, and review; not a substitute for manufacturer commissioning instructions.                             |
| IEEE Code of Ethics                                                                   | Supports prioritizing public safety, disclosing limitations, avoiding misleading claims, and reporting results honestly.                                                       |

### 6.3 Environmental and economic impact

The long-term motivation is to reduce commissioning time, improve repeatability, and potentially improve combustion performance. Those benefits remain hypotheses until validated. Because the prototype will not fire the burner, the project cannot claim measured reductions in fuel use, emissions, or carbon monoxide. Its valid performance claims are limited to software behavior, command/feedback performance, repeatability of the dry test workflow, quality of records, and comparison of workflow time under the test conditions.

### 6.4 Data integrity and cybersecurity

The software shall distinguish physical readings from simulated and replayed data. Configuration files and test records shall identify their versions. The prototype shall not accept undocumented remote commands or expose physical outputs to an unprotected network. Communication loss or corrupted input shall stop new optimization commands and trigger the documented fault behavior.

## 7. Resources

### 7.1 Budget estimate

The sponsor has supplied an intake assembly and blower. Their reference values are listed as in-kind resources rather than new cash expenses. The following budget is a planning estimate for the gas-free prototype. Actual costs will depend on the verified interfaces and the equipment available through the university.

| Subsystem / item                               | Purpose                                               | Estimated cash cost | Notes                                                                     |
|:-----------------------------------------------|:------------------------------------------------------|--------------------:|:--------------------------------------------------------------------------|
| Supervisory computer                           | UI, optimization, logging, reports                    |          \$80–\$180 | Raspberry Pi-class computer or equivalent; use lab hardware if available. |
| Isolated I/O / interface electronics           | Command and feedback conditioning                     |         \$150–\$450 | Final amount depends on actuator interface verification.                  |
| Microcontroller / pulse-generation board       | Deterministic timing if required                      |          \$25–\$100 | Conditional; omit if dedicated I/O handles timing.                        |
| Low-voltage power supplies, protection, fuses  | Safe power for interface and actuator circuits        |          \$75–\$200 | Based on verified voltage/current requirements.                           |
| Wiring, connectors, enclosure, terminal blocks | Prototype assembly and strain relief                  |         \$100–\$250 | Connector types to be confirmed.                                          |
| Blower tach/PWM conditioning                   | Speed command and feedback interface                  |          \$40–\$150 | May be included in I/O budget; avoid double counting at procurement.      |
| Measurement and verification tools             | DMM, oscilloscope/logic analyzer, current measurement |           \$0–\$250 | Assume university lab access; purchase only missing tools.                |
| Test fixture and mechanical guarding           | Secure mounting and pinch/rotation protection         |          \$50–\$200 | Use existing lab fixture where possible.                                  |
| Contingency                                    | Unknown interface needs and replacement parts         |         \$100–\$250 | Spend only with team/supervisor approval.                                 |
| **Estimated cash total**                       |                                                       |   **\$620–\$2,030** | Excludes equipment already available from sponsor/university.             |

**In-kind sponsor hardware (reference value, not cash expenditure)**

| Qty. | Part                                                  | Reference value | Budget treatment           |
|-----:|:------------------------------------------------------|----------------:|:---------------------------|
|    1 | Intake assembly, part 100304496 (proposal cost sheet) |           \$952 | Sponsor-provided           |
|    1 | A. O. Smith 8.9-inch blower, part 100144909           |           \$497 | Sponsor-provided           |
|      | **Total in-kind reference value**                     |     **\$1,449** | Not included in cash total |

The budget excludes a combustion analyzer because live combustion testing is outside the project scope. If the university or sponsor already has instrumentation that is useful for electrical and mechanical verification, it should be borrowed rather than purchased. The budget must be updated after the I/O inventory and interface prototype determine which signal-conditioning parts are actually required.

### 7.2 Division of labor

The proposal identifies four team members but does not provide verified individual skill assessments. The allocation below assigns primary ownership by subsystem and must be confirmed by the team based on demonstrated skills and workload. Every subsystem remains subject to peer review.

| Team member      | Primary responsibility                      | Supporting responsibility / deliverables                                                                   |
|:-----------------|:--------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Daniel Craig     | Actuator and blower electrical interface    | Connector/pinout inventory, signal-conditioning design, safe bench wiring, physical interface test record. |
| Charles Lutz     | Supervisory software and optimization logic | State machine, bounded candidate search, limit enforcement, unit tests, software versioning.               |
| Alex Marquez     | Data acquisition and validation             | Feedback scaling, simulation/replay inputs, dataset design, repeatability and validation metrics.          |
| Ethan Davenport  | Operator interface, logging, and reporting  | UI workflow, status/fault display, CSV/report generation, test documentation.                              |
| All team members | Integration and design review               | Requirements traceability, risk review, testing, final report, and presentation.                           |

**Required team skills and gaps:** The team shall complete a short skills inventory during the first design review. Embedded programming, electrical safety, controls, data analysis, and technical writing must each have a named primary and reviewer. Combustion expertise will be obtained from Lochinvar documentation and customer/supervisor consultation; the student team will not independently define live-combustion limits.

### 7.3 Timeline and Gantt chart

The schedule aligns with the proposal’s September 2026–April 2027 project window. Exact course milestones should replace these planning dates when confirmed. The highest-risk unknowns—actuator and blower electrical interfaces—are scheduled early because they constrain controller selection, wiring, and software implementation.

``` mermaid
gantt
    title Conceptual Design and Prototype Schedule
    dateFormat YYYY-MM-DD
    axisFormat %b %Y
    section Requirements
    Confirm scope, no-gas boundary, and safety plan :a1, 2026-09-15, 2026-10-23
    Inventory drawings, pinouts, and signal types :a2, 2026-10-12, 2026-11-06
    section Detailed Design
    Select controller and I/O after interface review :b1, 2026-10-26, 2026-11-13
    Design electrical interface and test fixture :b2, 2026-11-02, 2026-11-27
    Design state machine and optimization method :b3, 2026-10-26, 2026-11-27
    section Prototype
    Bench-test signals and feedback scaling :c1, 2026-11-23, 2026-12-18
    Build UI, logging, and replay data pipeline :c2, 2026-11-16, 2027-01-08
    Implement bounded optimizer and fault handling :c3, 2026-11-30, 2027-01-15
    section Integration and Validation
    Integrate hardware and software :d1, 2027-01-04, 2027-01-29
    Verify stop, timeout, and invalid-data cases :d2, 2027-01-18, 2027-02-12
    Repeatability and baseline workflow comparison :d3, 2027-02-01, 2027-03-05
    Fix defects and complete acceptance tests :d4, 2027-03-01, 2027-03-19
    section Final Deliverables
    Analyze results and finalize documentation :e1, 2027-03-08, 2027-04-02
    Final demonstration and presentation :e2, 2027-04-05, 2027-04-23
```

**Critical path and decision gates**

1.  **Gate 1 — Interface verification:** Confirm exact actuator and blower models, pinouts, electrical ratings, command method, and feedback signals. Do not purchase final I/O hardware before this gate.
2.  **Gate 2 — Safe bench operation:** Demonstrate supply protection, correct signal levels, position/tach feedback, and stop behavior with gas isolated and burner firing prevented.
3.  **Gate 3 — Software-in-the-loop:** Replay fixed datasets and prove command bounds, invalid-data handling, timeout behavior, logging, and report generation.
4.  **Gate 4 — Integrated gas-free prototype:** Demonstrate repeatable actuator/blower commands and feedback acquisition without any fuel or combustion.
5.  **Gate 5 — Acceptance review:** Compare the prototype against agreed criteria and document limitations. No live-combustion validation is included.

## 8. Verification and Acceptance Plan

| Test ID | Test                      | Method                                                                                                            | Evidence / acceptance basis                                                                                             |
|:--------|:--------------------------|:------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| V-01    | Configuration validation  | Provide missing, malformed, and out-of-range configuration values.                                                | Run is blocked and a clear error is logged.                                                                             |
| V-02    | Command-limit enforcement | Request commands below, within, and above configured bounds.                                                      | Out-of-range commands are rejected before reaching the interface.                                                       |
| V-03    | Feedback scaling          | Compare reported actuator position and blower speed with an independent reference instrument or verified fixture. | Error is measured and compared with a tolerance set before testing.                                                     |
| V-04    | Stop behavior             | Trigger stop during idle, command, and stabilization states.                                                      | New optimization commands stop and the documented output state is reached.                                              |
| V-05    | Communication timeout     | Disconnect or simulate loss of feedback/communications.                                                           | Fault is raised; no new optimization commands are issued.                                                               |
| V-06    | Invalid sensor data       | Inject stale, missing, NaN, and out-of-range simulated values.                                                    | Candidate is rejected and the reason is recorded.                                                                       |
| V-07    | Replay repeatability      | Run the same saved dataset and configuration multiple times.                                                      | Candidate sequence and decisions match deterministically, except explicitly nondeterministic fields such as timestamps. |
| V-08    | Physical repeatability    | Repeat bounded dry actuator/blower tests under the same conditions.                                               | Command/feedback variation is measured and reported against a pre-agreed tolerance.                                     |
| V-09    | Report integrity          | Compare report entries with raw log data.                                                                         | Values, units, source labels, configuration, and fault records agree.                                                   |
| V-10    | Workflow comparison       | Time the documented baseline workflow and prototype workflow for equivalent dry-test tasks.                       | Completion time and data completeness are reported; no unsupported combustion benefit is claimed.                       |

The team shall define numeric tolerances after component specifications and bench measurements are available. Until those values are agreed and recorded, a test can be described as completed but cannot be called a quantitative pass.

## 9. Risk Register

| Risk                                              | Consequence                                      | Mitigation                                                                                                      |
|:--------------------------------------------------|:-------------------------------------------------|:----------------------------------------------------------------------------------------------------------------|
| Incorrect actuator pinout or voltage              | Component damage or unexpected motion            | Verify exact part drawing and pinout; current-limited/appropriately protected initial tests; supervisor review. |
| Command signal incompatible with actuator         | No movement or uncontrolled movement             | Characterize interface before final controller selection; use dedicated driver/interface if needed.             |
| Unexpected mechanical motion                      | Pinch injury or fixture damage                   | Secure the assembly, guard pinch points, keep hands clear, and provide a stop control.                          |
| Blower AC voltage reaches low-voltage electronics | Electric shock or equipment damage               | Separate line-voltage and signal circuits; use rated isolation and protection.                                  |
| Incorrect tach scaling                            | Wrong speed estimate and misleading optimization | Verify pulses per revolution and compare against independent measurement.                                       |
| Stale or simulated data treated as physical       | Invalid conclusions                              | Source labels, freshness checks, report disclaimers, and automated tests.                                       |
| Software fault issues repeated commands           | Mechanical damage                                | Hard command bounds, timeouts, state-machine interlocks, and independent stop procedure.                        |
| Schedule delayed by unknown interface             | Late integration                                 | Make interface verification the first critical milestone; keep a software-in-the-loop path available.           |
| Overstated project results                        | Misleading customer or academic conclusions      | Limit claims to gas-free testing and clearly identify what has not been validated.                              |

## 10. Deliverables

The team will deliver:

1.  Verified hardware-interface inventory and wiring documentation.
2.  Gas-free actuator/blower interface prototype using the supplied hardware where feasible.
3.  Operator interface and explicit test-state machine.
4.  Bounded optimization and constraint-enforcement software.
5.  Simulation/replay dataset and regression tests.
6.  Timestamped data logging and report generation.
7.  Verification results for command bounds, feedback, stop behavior, timeouts, repeatability, and data integrity.
8.  Updated budget, risk register, and schedule.
9.  Final documentation identifying unresolved issues and limits of the prototype.
10. Demonstration of the gas-free prototype. The demonstration will not include gas, ignition, or live combustion.

## 11. References

1.  Lochinvar Corporation. *Crest Condensing Boiler with Hellcat Combustion Technology*. Product literature. Use the current manufacturer publication supplied by the customer.
2.  Lochinvar Corporation. *Hellcat Service Manual*, document 100343237 / 2000600598, Revision C (as identified in the project proposal). The current revision and applicable boiler model must be confirmed before relying on a procedure or limit.
3.  Siemens. *LMV37.400A / LMV37.420A Basic Unit with Integrated Air-Fuel Ratio Control for Forced-Draft Burners*, document A6V10095920. This document is a reference for evaluating candidate interfaces only; it does not establish compatibility with the supplied VA33 actuators.
4.  A. O. Smith / Lochinvar. Intake assembly and blower drawings supplied by the project sponsor, including intake assembly references 100304496 / 1001303976 as listed in project materials and blower 100144909. Part-number discrepancies shall be resolved against the physical labels and latest drawing.
5.  American Society of Heating, Refrigerating and Air-Conditioning Engineers (ASHRAE). *ASHRAE Handbook—Fundamentals*, chapter on combustion and fuels.
6.  ASHRAE. *ASHRAE Handbook—Systems and Equipment*, chapter on boilers.
7.  National Fire Protection Association. *NFPA 54: National Fuel Gas Code*. Current applicable edition to be confirmed by the customer/supervisor.
8.  National Fire Protection Association. *NFPA 70: National Electrical Code*. Current applicable edition to be confirmed by the laboratory/supervisor.
9.  CSA Group / ANSI. *CSA/ANSI Z21.13 / CSA 4.9: Gas-Fired Low-Pressure Steam and Hot Water Boilers*. Confirm the edition applicable to the actual host equipment.
10. International Electrotechnical Commission. *IEC 60204-1: Safety of Machinery—Electrical Equipment of Machines*. Apply where relevant to the prototype and laboratory.
11. ASHRAE. *Standard 202: The Commissioning Process*. Use as process guidance where relevant.
12. IEEE. *IEEE Code of Ethics*. Professional responsibility and truthful reporting guidance.

**Reference note:** Manufacturer-defined limits, wiring diagrams, and interface specifications shall be taken from the current documentation for the exact hardware supplied. Before final submission, the team should replace general document descriptions with exact revision numbers, publication dates, and stable URLs where available. No interface compatibility is asserted solely because two products are made by Siemens or appear in the same combustion system.

## 12. Statement of Contributions

Each team member must write and approve their own contribution statement. The following entries are placeholders. Each person must complete and approve their own statement before submission; the placeholders must not be treated as factual claims.

### Daniel Craig

\[To be completed by Daniel: describe the specific sections, design decisions, research, drawings, calculations, implementation, or testing personally completed.\]

### Charles Lutz

\[To be completed by Charles: describe the specific sections, design decisions, research, drawings, calculations, implementation, or testing personally completed.\]

### Alex Marquez

\[To be completed by Alex: describe the specific sections, design decisions, research, drawings, calculations, implementation, or testing personally completed.\]

### Ethan Davenport

\[To be completed by Ethan: describe the specific sections, design decisions, research, drawings, calculations, implementation, or testing personally completed.\]

Each team member shall ensure that their statement accurately describes their own contribution. The team shall review the final document for requirement traceability, technical accuracy, source quality, and consistency with the no-gas project boundary before submission.
