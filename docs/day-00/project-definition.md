# Day 0 — Project Definition

## Project Title

**Real-Time Digital Twin of a 355-V-Class Li-ion EV Battery Pack with C++ EKF State Estimation, IIoT/MQTT Communication, Secure Industrial HMI, and Web-Based Monitoring**

---

# 1. Problem Statement

Lithium-ion battery packs are one of the core systems in modern electric vehicles. Their condition directly affects vehicle performance, energy consumption, reliability, and operational safety. During real driving, the battery is constantly exposed to changing operating conditions. For example, current and power demand can rise quickly during acceleration, remain relatively steady while cruising, drop during idle periods, and even reverse direction during regenerative braking.

Although quantities such as pack voltage, current, and temperature can be measured directly, important internal battery states cannot be observed in the same way. **State of Charge (SOC)** is one of the most important examples. It has to be estimated from available measurements and an appropriate battery model.

A straightforward method such as Coulomb Counting can be useful for estimating SOC, but its error can accumulate over time. Its accuracy also depends strongly on the quality of the current measurement and on the initial SOC. In a real EV scenario, this limitation becomes more visible because the battery is rarely operating under a constant load. A meaningful test should therefore expose the model and estimator to realistic changes in power demand instead of relying mainly on randomly generated current data.

For this reason, the proposed system will use a **driving-cycle-based input**, so that the battery is tested under a sequence that resembles actual vehicle operation. A selected standard or representative drive cycle will first define vehicle speed. Vehicle speed will then be used to determine the required traction power and, consequently, the battery current. This produces a much more realistic operating sequence, including acceleration, cruising, braking, idle periods, and changes in load.

Regenerative braking is another important part of an EV-oriented battery model. During braking, part of the vehicle's kinetic energy can be recovered and returned to the battery. Depending on the selected sign convention, this appears as negative battery current. The model therefore needs to represent both battery discharge and regenerative charging, rather than treating current as a one-way input.

However, obtaining an SOC estimate is not enough on its own. The estimator also needs to be tested under conditions that show how it behaves in practice. An EKF may perform well when its initial state is already close to the actual state. A stronger evaluation should therefore also test an incorrect initial SOC, noisy measurements, and other controlled disturbances. The resulting estimate can then be evaluated quantitatively using metrics such as **MAE, RMSE, maximum error, and convergence time**.

The uncertainty information produced by the EKF is also useful. Instead of showing only a number such as `SOC = 84.2%`, the monitoring system should also give the operator an understandable indication of how uncertain that estimate is. This information can be derived from the EKF covariance and represented on the dashboard as an uncertainty or estimation-confidence indicator. The goal is therefore to show both what the estimator believes and how much uncertainty is associated with that estimate.

The system should also demonstrate how a real monitoring architecture behaves when something goes wrong. Because this project is a software prototype and does not require physical battery hardware, controlled **fault injection** will be used to reproduce abnormal conditions in a repeatable way. Examples include voltage sensor noise, sensor bias, abnormal temperature, cell-voltage deviation or imbalance, and communication loss. These scenarios will feed into the alarm-management layer, allowing abnormal conditions to be detected, classified, and clearly presented to the operator.

From a system-integration perspective, the battery model, estimator, Digital Twin, communication layer, HMI, and web application should work as parts of one connected system rather than as isolated software components. Together, they should form a single real-time data pipeline.

The proposed architecture is therefore based on the following flow:

**Driving Cycle → Vehicle Speed → Power Demand → Battery Current → Battery Model → EKF → Digital Twin → MQTT/IIoT → HMI/Web Monitoring**

At the pack level, the system will use an **Equivalent Pack Model** for the main real-time EKF to keep the computational load manageable. At the same time, the Digital Twin will preserve the higher-level structure of the system:

**Cell → Module → Pack → Vehicle**

This makes it possible to represent selected cell-level information for tasks such as imbalance detection, minimum/maximum cell-voltage monitoring, and abnormal-cell scenarios, without making the first version unnecessarily heavy by implementing a detailed electrochemical model for every cell.

The Digital Twin will also have two complementary operating modes.

In **Live Twin** mode, the system continuously processes incoming simulation or battery data and updates the digital representation in real time.

In **Analytical Twin** mode, the system can be used for comparison and analysis, such as:

**Actual SOC vs. Estimated SOC**

and

**Measured Voltage vs. Predicted Voltage**

This makes the Digital Twin more than a graphical representation of the battery. It becomes a software model that can follow the system while it is running and also support analysis during or after a run.

Finally, the monitoring architecture should not be purely passive. In addition to displaying telemetry, the web interface should be able to send controlled simulation commands—for example, starting or stopping a scenario, resetting the system, or selecting a different drive cycle. Before reaching the simulator, these commands must pass through authentication, validation, authorization, and the MQTT communication layer.

The main engineering problem of this project can therefore be stated as:

> **How can a real-time, connected, and secure Digital Twin be designed and implemented for a 355-V-class Li-ion EV battery pack so that realistic driving-cycle data can be converted into battery operating conditions, battery states can be estimated with a C++ EKF, estimator uncertainty and faults can be monitored, and the resulting information can be communicated, analyzed, and controlled through industrial and web-based interfaces?**

---

# 2. Objective

## 2.1. Primary Objective

The primary objective of this project is to design and implement a **working real-time Digital Twin prototype for a 355-V-class Li-ion EV battery pack** that can be demonstrated, tested, and measured end to end.

The final system should reproduce a realistic battery operating scenario, estimate the battery state in real time, maintain a synchronized digital representation of the pack, exchange data through MQTT, detect selected abnormal conditions, and provide both operator-oriented and web-based monitoring.

The project is intended to demonstrate a complete engineering pipeline, not simply a standalone EKF or a standalone dashboard:

**Drive Cycle → Vehicle Model Inputs → Battery Model → C++ EKF → Digital Twin → MQTT/IIoT → Secure HMI/Web Dashboard**

The system should also support controlled commands and repeatable demonstrations through simulation recording and replay.

## 2.2. Technical Objectives

The project will address the following technical objectives.

### 1. Develop a realistic EV operating input

Instead of generating battery current randomly, the system will use a driving cycle or another representative EV driving profile.

The input chain will be:

**Driving Cycle → Vehicle Speed → Power Demand → Battery Current**

The resulting battery current will expose the model to acceleration, cruising, braking, idle conditions, and varying load.

### 2. Model regenerative braking

The battery model will support both charging and discharging behavior.

During normal traction:

**Battery → Vehicle**

During regenerative braking:

**Vehicle → Battery**

The model must therefore allow negative battery current where the selected convention represents regenerative charging.

### 3. Develop the battery electrical model

A suitable electrical/mathematical battery model will be developed to represent the relevant dynamic behavior of the 355-V-class pack.

The model should provide the quantities required by the estimator and monitoring system while remaining simple enough for real-time execution.

### 4. Define the battery state variables

The state vector required by the selected model will be defined, with **SOC as the primary state of interest**.

Additional internal/model states will be included when they are required by the chosen equivalent-circuit model.

### 5. Implement a C++ Extended Kalman Filter

An **EKF will be formulated and implemented in C++** for real-time state estimation.

The implementation will include:

- State prediction
- Covariance prediction
- Measurement update
- Gain calculation
- State correction
- Covariance update
- Initialization and reset
- Real-time execution

### 6. Evaluate EKF behavior under controlled scenarios

The estimator will not be judged using only one ideal test case.

At least the following scenarios will be considered:

**Scenario A — Correct Initial SOC**

Actual SOC = 80%
Initial EKF SOC = 80%

**Scenario B — Incorrect Initial SOC**

Actual SOC = 80%
Initial EKF SOC = 60%

**Scenario C — Noisy Measurements**

Voltage and/or current measurements include controlled measurement noise.

Additional scenarios may be introduced when useful, including sensor bias, changing temperature, or other model mismatch conditions.

The evaluation will consider:

- MAE
- RMSE
- Maximum absolute error
- Convergence time
- Estimator response under changing operating conditions

### 7. Use EKF covariance as an uncertainty indicator

The system will retain relevant covariance information from the EKF.

For example:

`cov_trace`

can be used as one of the signals for an uncertainty indicator.

The dashboard will therefore show not only the estimated SOC but also an indication of the associated estimation uncertainty, using a clearly defined interpretation rather than presenting an unsupported numerical "confidence percentage."

### 8. Build a hierarchical battery representation

The Digital Twin architecture will conceptually represent the battery as:

**Cell → Module → Pack → Vehicle**

The main EKF will initially operate on an **Equivalent Pack Model**.

Selected cell-level variables will still be represented for monitoring and fault scenarios, including:

- Minimum cell voltage
- Maximum cell voltage
- Cell voltage spread
- Cell imbalance
- Abnormal cell identification

This approach keeps the real-time estimation problem manageable while preserving a meaningful EV battery architecture.

### 9. Build the Digital Twin

The Digital Twin will continuously synchronize with the incoming battery data and estimator outputs.

It should represent:

- Current battery operating condition
- SOC
- Internal/model states
- Voltage and current behavior
- Temperature
- Cell/module/pack information where available
- Estimation uncertainty
- Fault and alarm status

### 10. Provide two Digital Twin modes

The system will support two main modes.

**Live Twin**

Real-time operation:

**Simulation/Data Source → EKF → MQTT → Digital Twin → Monitoring**

**Analytical Twin**

Analysis and comparison of:

- Actual SOC vs. Estimated SOC
- Measured Voltage vs. Predicted Voltage
- Estimation error
- Estimator uncertainty
- System response during selected events

### 11. Implement IIoT communication using MQTT

MQTT will be the primary messaging layer.

The communication architecture will include:

- MQTT Broker
- Publisher/Subscriber structure
- Telemetry topics
- State-estimation topics
- Alarm/fault topics
- Command topics
- Command acknowledgement topics
- Defined payload structures
- Update rates
- Connection monitoring
- Reconnection handling

A representative topic structure may include:

`riri/bms/telemetry`

`riri/bms/command`

`riri/bms/command/ack`

with the final namespace and topic hierarchy being defined during implementation.

### 12. Implement fault injection

The system will support controlled fault scenarios without requiring physical battery faults.

Examples include:

- Voltage sensor noise
- Current sensor noise
- Sensor bias
- Abnormally high temperature
- Cell over-voltage
- Cell under-voltage
- Excessive cell-voltage difference
- Cell imbalance
- Communication loss
- MQTT disconnection

Faults will be introduced in a controlled and repeatable way so that their effect on the estimator, Digital Twin, communication layer, and HMI can be evaluated.

### 13. Implement alarm management

The monitoring system will classify detected conditions into three clear severity levels:

**NORMAL**

**WARNING**

**CRITICAL**

Examples include high temperature as a warning condition and cell over-voltage or communication failure as critical conditions when the configured limits are exceeded.

The alarm layer should provide:

- Alarm detection
- Severity classification
- Active-alarm indication
- Alarm message
- Relevant measurement
- Timestamp
- State change/update
- Acknowledgement where appropriate

### 14. Develop a secure industrial HMI

The industrial HMI will be designed around the principles of **High-Performance HMI**, with reference to the **ANSI/ISA-101** approach used for industrial HMI design.

The design will prioritize:

- Clear hierarchy
- Low visual clutter
- Consistent navigation
- Trend visualization
- Alarm prioritization
- Strong indication of abnormal conditions
- Minimal decorative elements
- Consistent interaction patterns

The HMI should help an operator recognize abnormal behavior quickly; visual attractiveness is secondary to clarity and usability.

### 15. Develop a web-based monitoring dashboard

The web application will provide remote monitoring of the system.

The dashboard will include, as appropriate:

- SOC
- SOC uncertainty indicator
- Pack voltage
- Pack current
- Temperature
- Cell minimum/maximum voltage
- Cell imbalance information
- System status
- Active alarms
- Time-series trends
- EKF performance
- Actual vs. estimated comparison
- Measured vs. predicted voltage
- Communication status

### 16. Add Live and Replay modes

The monitoring system will support:

**LIVE MODE**

for real-time operation, and

**REPLAY MODE**

for playback of previously recorded simulation runs.

A recorded scenario such as:

`Drive Cycle #01`

can later be loaded and streamed again so that the same data flow is reproduced through the Digital Twin and dashboard.

The replay capability will make demonstrations repeatable and will also provide a practical way to compare different estimator settings and system versions.

### 17. Add controlled simulation commands

The web interface will not be limited to passive visualization.

Selected commands may include:

- Start Simulation
- Stop Simulation
- Reset
- Change Drive Cycle
- Start Replay
- Stop Replay

The command path will follow:

**Web UI**

↓

**Authentication**

↓

**Command Validation**

↓

**Authorization**

↓

**MQTT**

↓

**Simulator**

Commands should be restricted to explicitly permitted operations and should not bypass the system's control and validation layers.

### 18. Implement basic cybersecurity mechanisms

Security will be treated as an actual part of the system architecture, not merely as a section in the documentation.

The prototype will consider mechanisms such as:

- Client authentication
- TLS-protected MQTT communication
- MQTT ACLs
- WSS for web communication where applicable
- Command authorization
- Payload validation
- Controlled access to configuration and command functions

The security implementation will remain appropriate to a prototype environment and will not be presented as production automotive cybersecurity certification.

### 19. Validate the end-to-end system

The final system will be tested as one connected architecture rather than as a collection of independent modules.

Validation will consider:

- Estimation accuracy
- Convergence behavior
- Real-time response
- Drive-cycle response
- Regenerative braking behavior
- Fault response
- Alarm response
- Communication reliability
- Digital Twin synchronization
- Replay consistency
- Command-path behavior
- Monitoring performance
- Security controls

---

# 3. Scope

## 3.1. In Scope

### A. EV Driving Cycle and Operating Scenario

The project will include a driving-cycle-based source for generating realistic battery operating conditions.

The general flow will be:

**Driving Cycle**

↓

**Vehicle Speed**

↓

**Power Demand**

↓

**Battery Current**

↓

**Battery Pack Model**

The selected cycle will include representative operating regions such as:

- Acceleration
- Cruising
- Braking
- Idle
- Variable load

The purpose is to avoid using artificial random current as the main representation of vehicle operation.

The system will also support regenerative braking so that battery charging behavior can be represented during suitable braking events.

---

### B. Battery Pack Model

The project focuses on a **355-V-class Li-ion EV battery pack** or an equivalent representative model.

The battery representation will include the measurements and variables required for estimation and monitoring:

- Pack voltage
- Pack current
- Temperature
- SOC
- Relevant internal/model states
- Selected cell-level information

The conceptual hierarchy is:

**Cell → Module → Pack → Vehicle**

For the primary real-time estimator, the battery will initially be represented using an **Equivalent Pack Model**.

Cell-level behavior will be introduced selectively for monitoring and fault scenarios instead of building a full high-fidelity cell-by-cell electrochemical model.

The project focuses on modeling, monitoring, state estimation, and software integration. It does not include physically manufacturing a high-voltage battery pack.

---

### C. Digital Twin

The Digital Twin will provide a software representation of the battery system and its current operating condition.

It will:

- Receive real-time measurements
- Update the battery model
- Maintain the current digital state
- Integrate EKF estimates
- Track estimation uncertainty
- Represent selected cell/module/pack information
- Track alarms and faults
- Maintain synchronization with the system data stream

The Digital Twin will have two main operational perspectives:

**Live Twin** — follows the system in real time.

**Analytical Twin** — supports comparison and post-run analysis.

The Analytical Twin may be used to compare:

**Actual SOC vs. Estimated SOC**

and:

**Measured Voltage vs. Predicted Voltage**

along with estimation error and uncertainty.

---

### D. Battery State Estimation

State estimation is a core part of the project.

The scope includes:

- State-space battery modeling
- State-vector definition
- EKF formulation
- C++ implementation
- Real-time prediction
- Real-time correction
- SOC estimation
- Covariance tracking
- Estimation error analysis
- Reference comparison
- Noise testing
- Initial-condition testing

The estimator will be evaluated under controlled conditions rather than only under an ideal initial state.

---

### E. Fault Injection and Abnormal Scenarios

Controlled fault injection is included as part of the simulation and validation layer.

The system will be able to reproduce selected abnormal conditions such as:

**Sensor-related faults**

- Voltage noise
- Current noise
- Sensor bias

**Battery-related faults**

- High temperature
- Cell over-voltage
- Cell under-voltage
- Cell imbalance
- Abnormal cell voltage
- Excessive voltage spread

**Communication-related faults**

- MQTT disconnection
- Delayed or missing messages
- Communication interruption

Fault injection is intended to test how the monitoring and response layers behave under controlled abnormal conditions. It does not represent a physical failure of a real battery pack.

---

### F. Alarm Management

The project includes an alarm layer with three primary severity states:

| Level | Meaning |
| ------------ | --------------------------------------------------------------------------------------- |
| **NORMAL** | Parameters are within configured operating limits |
| **WARNING** | An abnormal condition requires attention but is not yet treated as critical |
| **CRITICAL** | A condition requires immediate attention or represents a significant system abnormality |

The alarm system will provide a clear indication of:

- Active alarms
- Severity
- Alarm source
- Relevant value
- Timestamp
- Current condition

The final threshold values will be defined according to the selected model, simulation assumptions, and project requirements.

---

### G. IIoT / MQTT Communication

MQTT will be used as the main messaging protocol.

The communication architecture will include:

- MQTT Broker
- Publisher/Subscriber components
- Telemetry topics
- Estimator/state topics
- Alarm/fault topics
- Command topics
- Acknowledgement topics
- Defined message structures
- Update rates
- Connection monitoring
- Reconnection handling

The architecture will separate telemetry and control traffic.

Representative topics include:

`riri/bms/telemetry`

`riri/bms/command`

`riri/bms/command/ack`

The exact payload schema will be defined during the implementation phase.

---

### H. Secure Industrial HMI

An industrial HMI will be developed for operator-level monitoring.

It will display relevant information such as:

- Pack voltage
- Pack current
- Temperature
- SOC
- Estimation uncertainty
- Cell minimum/maximum voltage
- Cell imbalance
- System status
- Communication status
- EKF outputs
- Active alarms
- Warnings
- Critical conditions

The HMI will follow a high-performance industrial visualization approach, with emphasis on clear hierarchy and abnormal-condition visibility rather than decorative design.

The HMI may also expose selected authorized controls where required by the simulation architecture.

---

### I. Web-Based Monitoring

The web dashboard will provide remote system visibility.

The dashboard will include the main live values and analytical views required to understand the system:

- KPI information
- Live measurements
- SOC
- Estimation uncertainty
- Time-series plots
- Actual vs. estimated SOC
- Measured vs. predicted voltage
- System state
- Cell-level summary
- Alarm status
- Communication status
- Historical data
- Replay controls

The web application will consume data from the backend/communication layer rather than directly interacting with low-level battery hardware.

---

### J. Replay System

The system will support recording and replaying selected simulation runs.

A run such as:

**Drive Cycle #01**

can be recorded and stored with its associated telemetry and state information.

Later, the system can enter:

**REPLAY MODE**

and stream the recorded data again through the same monitoring pipeline.

The goal is to create a reproducible test and demonstration environment.

---

### K. Command and Control Simulation

The web interface will support a limited set of simulation commands.

Examples include:

- Start simulation
- Stop simulation
- Reset
- Select drive cycle
- Start replay
- Stop replay

The command path will be:

**Web UI → Authentication → Validation → Authorization → MQTT → Simulator**

Each command must be validated before being accepted by the simulator.

The telemetry path and command path will remain logically separate.

---

### L. Security

The project includes prototype-level security measures.

The intended architecture may include:

**Client Authentication**

-

**TLS**

-

**MQTT ACL**

-

**WSS**

-

**Command Authorization**

-

**Payload Validation**

Security mechanisms will focus on protecting communication, restricting access, and preventing unauthorized or malformed commands from reaching the simulation layer.

---

### M. System Integration and Validation

The final system will connect the following layers:

**Driving Cycle**

↓

**Vehicle Speed**

↓

**Power Demand**

↓

**Battery Pack Model**

↓

**Measurements**

↓

**C++ EKF**

↓

**Digital Twin**

↓

**MQTT / IIoT**

↓

**Industrial HMI**

↓

**Web Dashboard**

with a controlled command path in the opposite direction:

**Web UI → Secure Command Path → MQTT → Simulator**

The validation layer will test both normal and abnormal operation.

---

## 3.2. Out of Scope

The following are outside the primary scope of the first implementation:

- Physical manufacturing of a high-voltage EV battery pack
- Physical EV drivetrain implementation
- Production BMS PCB design and manufacturing
- High-voltage power electronics design
- Detailed electrochemical modeling of every cell
- Full cell-level EKF estimation for the entire pack
- Automotive functional-safety certification
- Production-level automotive cybersecurity certification
- Commercial deployment
- Large-scale cloud infrastructure
- Fully autonomous battery control
- Advanced ML/DL battery prognostics
- Full SOH estimation
- Full Remaining Useful Life (RUL) prediction
- Advanced predictive maintenance
- Advanced fault diagnosis beyond the defined prototype scenarios

These topics can be considered as future extensions once the core architecture is stable.

---

# 4. Constraints

## 4.1. Battery and Modeling Constraints

The battery model is a simplified representation of a real EV battery and cannot reproduce every electrochemical phenomenon.

Battery parameters may vary with:

- SOC
- Temperature
- Current
- Aging
- Operating conditions

The accuracy of the Digital Twin therefore depends on the quality of the selected model and its parameters.

The 355-V-class value represents a **nominal voltage class**, not a constant operating voltage.

Cell-to-cell differences, internal resistance variation, and cell imbalance may not be represented with full physical fidelity in the first version.

The use of an Equivalent Pack Model for the EKF also means that the estimator will not reproduce every individual cell behavior.

---

## 4.2. Driving-Cycle Constraints

A drive cycle is only a representation of real driving behavior.

The quality of the resulting battery load depends on:

- The selected driving profile
- Vehicle assumptions
- Vehicle mass and resistance parameters
- Efficiency assumptions
- Auxiliary loads
- Battery model parameters

Therefore, the simulated battery current should be interpreted as the output of the selected vehicle-and-battery model, not as a direct measurement from a physical EV.

The model must also use a consistent sign convention so that discharge current and regenerative charging current are not confused.

---

## 4.3. EKF Constraints

EKF performance depends on:

- The selected battery model
- State initialization
- Initial covariance
- Process-noise covariance
- Measurement-noise covariance
- Parameter identification
- Measurement quality
- Model mismatch
- Numerical implementation

An EKF is not guaranteed to converge under every possible combination of bad initialization, severe noise, or incorrect model parameters.

The implementation must also remain computationally efficient enough to operate in real time.

Covariance values will be used to represent estimator uncertainty, but the system should not interpret covariance directly as a percentage confidence without a defined statistical mapping.

---

## 4.4. Data Constraints

The project may rely on simulated data, experimental data, or a combination of both.

Available datasets may contain:

- Sensor noise
- Missing samples
- Different sampling rates
- Timing inconsistencies
- Limited operating conditions
- Uncertain reference values

A reliable reference SOC is required for meaningful quantitative evaluation.

For simulated scenarios, the actual model SOC can act as a reference, but this should be clearly distinguished from a physically measured ground truth.

The quality of the Digital Twin will therefore be constrained by the quality and coverage of the available data.

---

## 4.5. Fault-Injection Constraints

Fault injection is intentionally artificial.

An injected sensor bias or abnormal cell voltage does not reproduce every physical mechanism that would cause the same fault in a real battery.

The fault layer is therefore intended to test:

- Detection logic
- Alarm generation
- Data handling
- Visualization
- System response
- Communication behavior

rather than to claim physical validation of real battery failure mechanisms.

---

## 4.6. Communication Constraints

MQTT communication depends on network availability, Broker behavior, and message handling.

Potential issues include:

- Latency
- Packet loss
- Connection loss
- Delayed messages
- Duplicate messages
- Out-of-order data
- Invalid payloads

The system will therefore require consistent message structures and basic reconnection handling.

Communication reliability will be evaluated within the limits of the prototype environment.

---

## 4.7. Security Constraints

The security architecture is designed for an engineering prototype rather than a production automotive system.

TLS, authentication, ACLs, WSS, command authorization, and payload validation can significantly improve the security of the system, but they do not constitute full automotive cybersecurity compliance or certification.

The project does not claim compliance with a complete production cybersecurity framework.

Security testing will focus on the attack surfaces introduced by the prototype architecture, particularly telemetry access, command handling, authentication, and communication channels.

---

## 4.8. HMI and Visualization Constraints

The HMI must remain readable as the number of monitored signals increases.

A large number of KPIs, alarms, graphs, and indicators can reduce usability rather than improve it.

The visualization must therefore balance:

**Information Density ↔ Operator Readability**

Abnormal conditions should be visually distinguishable without relying on excessive colors, decorative graphics, gradients, or unnecessary animation.

The web dashboard also has to remain responsive during real-time streaming and replay.

---

## 4.9. Replay Constraints

Replay Mode depends on the availability and integrity of recorded runs.

A replay should preserve the timing information needed to reproduce the original data stream reasonably closely.

Replay is intended for:

- Demonstration
- Repeatable testing
- Debugging
- Comparison of algorithm versions
- HMI and communication testing

It should not be confused with a new physical experiment.

---

## 4.10. Command and Control Constraints

Commands initiated from the web interface create a second communication direction and therefore introduce additional security and reliability requirements.

A command must not be accepted only because it was received.

The system should verify:

**Who sent it → Is the command allowed → Is the payload valid → Is the simulator in a valid state → Should the command be executed?**

Commands must therefore pass through authentication, validation, and authorization before execution.

The command interface will remain limited to simulation and monitoring functions and will not directly control physical high-voltage equipment.

---

## 4.11. Computational Constraints

The architecture combines several real-time components:

- Battery model
- EKF
- Fault injection
- MQTT communication
- Digital Twin updates
- HMI
- Web dashboard
- Replay engine

The system must therefore be designed so that monitoring and visualization do not interfere with the timing requirements of the estimator.

The main estimator should remain computationally lightweight enough for real-time execution, which is one of the reasons the initial EKF is based on an Equivalent Pack Model rather than a full cell-level estimator.

---

## 4.12. Project-Level Constraints

This project is defined as a **Research/Engineering Prototype and Proof of Concept (PoC)** rather than a production automotive system.

The priority is to build a complete, testable architecture first and only then increase its complexity.

The system should remain modular so that components such as:

- Battery Model
- Drive-Cycle Generator
- EKF
- Fault Injector
- MQTT Layer
- Digital Twin
- Alarm Manager
- HMI
- Web Dashboard
- Replay Engine

can be modified or replaced without redesigning the entire system.

The final implementation must balance:

**Estimation Accuracy**

-

**Real-Time Performance**

-

**EV Realism**

-

**Communication Reliability**

-

**Security**

-

**Usability**

-

**Development Complexity**

The project should therefore prioritize a **working, measurable, reproducible, and technically defensible system** rather than adding features simply to make the project appear more complex.

---

# Final Day 0 System Boundary

## Inputs

**Driving Cycle + Vehicle Parameters + Battery Parameters + Measurement Data + Simulation Commands**

## Core Processing

**Vehicle/Power Model + Battery Model + C++ EKF + Fault Injection + Digital Twin + Alarm Manager + MQTT/IIoT Layer**

## Outputs

**Estimated SOC + Estimation Uncertainty + Battery State + Fault/Alarm Status + Industrial HMI + Web Dashboard + Replay/Analytical Results**

## Data Flow

**Driving Cycle**

↓

**Vehicle Speed**

↓

**Power Demand**

↓

**Battery Current**

↓

**Battery Pack Model**

↓

**Voltage / Current / Temperature**

↓

**C++ EKF**

↓

**SOC + Model States + Covariance**

↓

**Digital Twin**

↓

**MQTT / IIoT**

↓

**Industrial HMI + Web Dashboard**

## Command Flow

**Web Dashboard**

↓

**Authentication**

↓

**Command Validation**

↓

**Authorization**

↓

**MQTT Command**

↓

**Simulator**

↓

**Command ACK**

## Demonstration Modes

**LIVE MODE**

Real-time simulation and monitoring.

**REPLAY MODE**

Reproduce a previously recorded scenario.

**ANALYTICAL MODE**

Compare actual/reference and estimated behavior, including:

**Actual SOC vs. Estimated SOC**

**Measured Voltage vs. Predicted Voltage**

**Estimation Error**

**EKF Uncertainty**

## Core Deliverable

> **A reproducible, real-time EV battery Digital Twin prototype that connects realistic driving-cycle simulation, battery state estimation, EKF uncertainty, fault injection, MQTT/IIoT communication, secure command handling, industrial HMI, web monitoring, alarm management, and replay-based analysis into one end-to-end system.**
