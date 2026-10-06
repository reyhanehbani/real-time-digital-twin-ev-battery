# Day 0 — Project Definition

## Project Title

**BMS-Oriented Real-Time Digital Twin of a 355-V-Class Li-ion EV Battery Pack with C++ EKF State Estimation, IIoT/MQTT Communication, Secure Industrial HMI, and Web-Based Monitoring**

---

# 1. Problem Statement

Lithium-ion battery packs are one of the most critical systems in modern electric vehicles. Their condition directly affects vehicle performance, energy consumption, reliability, and operational safety.

A **Battery Management System (BMS)** is responsible for continuously monitoring the battery and estimating internal states that cannot be measured directly. Important BMS functions include battery voltage, current, and temperature monitoring, State of Charge (SOC) estimation, state monitoring, abnormal-condition detection, alarm management, and safe operating supervision.

During real EV operation, the battery is exposed to continuously changing operating conditions. Current and power demand can increase rapidly during acceleration, remain relatively stable during cruising, decrease during idle periods, and reverse direction during regenerative braking.

Although quantities such as pack voltage, current, and temperature can be measured directly, important internal battery states cannot be observed in the same way. **State of Charge (SOC)** is one of the most important examples. Therefore, a BMS requires a suitable battery model and state-estimation algorithm to determine the internal state of the battery from available measurements.

A straightforward method such as Coulomb Counting can provide an SOC estimate, but its error can accumulate over time and its accuracy depends strongly on current measurement quality and initial SOC. A more robust BMS-oriented approach is therefore required.

For this reason, the proposed system will implement a **model-based battery state estimation subsystem using an Extended Kalman Filter (EKF)**. The EKF will operate in real time and estimate the battery state from voltage, current, and other available measurements.

The battery will not be tested using only randomly generated current data. Instead, the system will use a **driving-cycle-based operating scenario**:

**Driving Cycle → Vehicle Speed → Power Demand → Battery Current**

This produces a more realistic battery operating sequence containing acceleration, cruising, braking, idle periods, and changing load conditions.

Regenerative braking will also be represented. During braking, part of the vehicle's kinetic energy can be recovered and returned to the battery. Depending on the selected sign convention, regenerative braking appears as negative battery current. The battery model must therefore represent both discharge and regenerative charging.

The project is primarily a **BMS-oriented software and simulation prototype** rather than a physical BMS hardware implementation. The BMS core will be responsible for battery measurement processing, state estimation, abnormal-condition detection, and battery-state monitoring.

The system will use an **Equivalent Pack Model** for the main real-time EKF to maintain manageable computational complexity. At the same time, the battery architecture will preserve the conceptual hierarchy:

**Cell → Module → Pack → Vehicle**

Selected cell-level information will be represented for BMS monitoring functions such as:

- Minimum cell voltage
- Maximum cell voltage
- Cell-voltage spread
- Cell imbalance
- Abnormal cell identification

The BMS will also be evaluated under controlled abnormal scenarios. Because this project does not require physical high-voltage battery hardware, **fault injection** will be used to reproduce abnormal conditions in a repeatable software environment.

Examples include:

- Voltage sensor noise
- Current sensor noise
- Sensor bias
- Abnormally high temperature
- Cell over-voltage
- Cell under-voltage
- Cell imbalance
- Excessive cell-voltage difference
- Communication loss

The BMS monitoring and fault-management layers will classify abnormal conditions and provide appropriate alarms.

The project will then extend the BMS through a **Digital Twin layer**. The Digital Twin will maintain a synchronized software representation of the battery and BMS state, allowing the system to operate in real time and support analytical comparison after or during a run.

The complete architecture therefore consists of a BMS core surrounded by Digital Twin, communication, monitoring, and application layers:

**EV Operating Scenario → Battery Model → BMS Measurement Layer → BMS State Estimation → BMS Fault Management → Digital Twin → MQTT/IIoT → HMI/Web Monitoring**

The system should also provide controlled simulation commands through a secure communication path. Commands such as starting or stopping a simulation, resetting the system, selecting a drive cycle, or starting replay must pass through authentication, validation, authorization, and MQTT communication before reaching the simulator.

The main engineering problem can therefore be stated as:

> **How can a real-time BMS-oriented system be designed and implemented for a 355-V-class Li-ion EV battery pack so that realistic driving-cycle data can be converted into battery operating conditions, battery states can be estimated using a C++ EKF, battery abnormalities can be detected and classified, and the resulting BMS state can be synchronized through a Digital Twin and communicated to secure industrial and web-based monitoring interfaces?**

---

# 2. Objective

## 2.1. Primary Objective

The primary objective of this project is to design and implement a **working real-time BMS-oriented Digital Twin prototype for a 355-V-class Li-ion EV battery pack**.

The system will reproduce realistic EV battery operating conditions, monitor battery measurements, estimate battery states in real time, detect selected abnormal conditions, maintain a synchronized Digital Twin, exchange information through MQTT, and provide operator-oriented and web-based monitoring.

The project is therefore not intended to be only an EKF implementation or only a Digital Twin visualization.

The core engineering pipeline is:

**Driving Cycle → Vehicle/Power Model → Battery Model → BMS Measurements → C++ EKF → BMS State & Fault Management → Digital Twin → MQTT/IIoT → HMI/Web Dashboard**

The project will demonstrate how the core functions of a software-based BMS can be integrated into a complete real-time engineering system.

---

## 2.2. BMS-Centered Technical Objectives

### 1. Develop a realistic EV operating input

The system will use a driving cycle or representative EV driving profile rather than random battery current.

The input chain will be:

**Driving Cycle → Vehicle Speed → Power Demand → Battery Current**

---

### 2. Develop the battery pack model

A suitable electrical/mathematical model will be developed for a 355-V-class Li-ion battery pack.

The model must provide the measurements and internal states required by the BMS estimator and monitoring system.

---

### 3. Implement BMS measurement processing

The BMS measurement layer will process the primary battery measurements:

- Pack voltage
- Pack current
- Temperature
- Cell voltages where available

These measurements will provide the inputs required by the BMS state-estimation and fault-management functions.

---

### 4. Implement SOC estimation

**State of Charge (SOC)** will be the primary estimated battery state.

The system will use a model-based estimation approach rather than relying only on Coulomb Counting.

---

### 5. Implement a C++ Extended Kalman Filter

An **EKF will be formulated and implemented in C++** for real-time battery state estimation.

The implementation will include:

- State prediction
- Covariance prediction
- Measurement update
- Gain calculation
- State correction
- Covariance update
- Initialization
- Reset
- Real-time execution

The EKF will constitute one of the core computational components of the BMS.

---

### 6. Evaluate EKF performance

The estimator will be evaluated using controlled scenarios rather than only an ideal operating condition.

At minimum:

**Scenario A — Correct Initial SOC**

Actual SOC = 80%

Initial EKF SOC = 80%

**Scenario B — Incorrect Initial SOC**

Actual SOC = 80%

Initial EKF SOC = 60%

**Scenario C — Noisy Measurements**

Voltage and/or current measurements contain controlled noise.

Performance will be evaluated using:

- MAE
- RMSE
- Maximum absolute error
- Convergence time
- Response under changing operating conditions

---

### 7. Implement estimation uncertainty monitoring

The BMS will retain relevant EKF covariance information.

For example:

`cov_trace`

may be used as an indicator of estimator uncertainty.

The monitoring system will therefore show both:

**Estimated SOC**

and

**Estimation Uncertainty**

The system will not present covariance directly as a numerical confidence percentage without an appropriate statistical interpretation.

---

### 8. Implement BMS battery monitoring

The BMS monitoring subsystem will continuously track:

- Pack voltage
- Pack current
- Temperature
- SOC
- Minimum cell voltage
- Maximum cell voltage
- Cell-voltage spread
- Cell imbalance
- Relevant battery-model states

This monitoring layer forms the primary interface between the battery model and the higher-level Digital Twin.

---

### 9. Implement battery fault detection

The BMS will detect selected abnormal conditions including:

- Over-voltage
- Under-voltage
- Over-temperature
- Excessive current
- Cell imbalance
- Abnormal cell voltage
- Excessive voltage spread
- Sensor abnormalities
- Communication abnormalities

The fault-management system will classify detected conditions and generate alarms.

---

### 10. Implement a hierarchical battery representation

The Digital Twin and BMS architecture will conceptually represent:

**Cell → Module → Pack → Vehicle**

The primary real-time EKF will initially operate on an **Equivalent Pack Model**.

Selected cell-level variables will still be represented for BMS monitoring and fault scenarios.

---

### 11. Build the Digital Twin

The Digital Twin will provide a synchronized software representation of the battery and BMS state.

It will represent:

- Current battery operating condition
- SOC
- Internal/model states
- Voltage
- Current
- Temperature
- Cell/module/pack information where available
- Estimation uncertainty
- Fault status
- Alarm status
- BMS operating state

The Digital Twin is therefore an extension of the BMS rather than a replacement for it.

---

### 12. Provide Live and Analytical Twin modes

**Live Twin**

The system follows the BMS and battery state in real time:

**Battery Model → BMS → Digital Twin → Monitoring**

**Analytical Twin**

The system supports comparison and post-run analysis such as:

- Actual SOC vs. Estimated SOC
- Measured Voltage vs. Predicted Voltage
- Estimation Error
- Estimation Uncertainty
- Battery response during selected events

---

### 13. Implement IIoT communication using MQTT

MQTT will provide the communication layer between the BMS/Digital Twin and higher-level applications.

The communication architecture will include:

- MQTT Broker
- Publisher/Subscriber structure
- Telemetry topics
- BMS state-estimation topics
- Alarm/fault topics
- Command topics
- Command acknowledgement topics
- Connection monitoring
- Reconnection handling

Representative topics may include:

`riri/bms/telemetry`

`riri/bms/state`

`riri/bms/alarm`

`riri/bms/command`

`riri/bms/command/ack`

---

### 14. Implement fault injection

Controlled fault scenarios will be introduced without physical battery faults.

Examples:

- Voltage sensor noise
- Current sensor noise
- Sensor bias
- High temperature
- Cell over-voltage
- Cell under-voltage
- Cell imbalance
- Abnormal cell voltage
- Excessive voltage spread
- MQTT disconnection

The purpose is to evaluate BMS detection, alarm management, Digital Twin behavior, communication, and HMI response.

---

### 15. Implement BMS alarm management

The alarm-management subsystem will classify conditions into:

**NORMAL**

**WARNING**

**CRITICAL**

Each alarm should contain relevant information such as:

- Alarm source
- Severity
- Measurement
- Timestamp
- Current condition
- State change
- Acknowledgement where appropriate

---

### 16. Develop a secure industrial HMI

The industrial HMI will provide operator-level visibility into the BMS.

It will display information such as:

- Pack voltage
- Pack current
- Temperature
- SOC
- SOC uncertainty
- Minimum/maximum cell voltage
- Cell imbalance
- BMS status
- Fault status
- Active alarms
- Communication status
- EKF outputs

The HMI will follow High-Performance HMI principles and prioritize clarity and abnormal-condition visibility over decorative design.

---

### 17. Develop a web-based BMS monitoring dashboard

The web application will provide remote monitoring of the BMS and Digital Twin.

The dashboard may include:

- SOC
- SOC uncertainty
- Pack voltage
- Pack current
- Temperature
- Minimum/maximum cell voltage
- Cell imbalance
- BMS state
- Active alarms
- Time-series trends
- EKF performance
- Actual vs. estimated SOC
- Measured vs. predicted voltage
- Communication status
- Historical data

---

### 18. Add Live and Replay modes

The monitoring system will support:

**LIVE MODE**

Real-time BMS operation.

**REPLAY MODE**

Playback of previously recorded BMS/simulation data.

Replay will support:

- Demonstration
- Repeatable testing
- Debugging
- Algorithm comparison
- HMI testing
- Communication testing

---

### 19. Add controlled simulation commands

The web interface may provide controlled simulation commands:

- Start Simulation
- Stop Simulation
- Reset
- Change Drive Cycle
- Start Replay
- Stop Replay

The command path will be:

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

The command system will not directly control physical high-voltage equipment.

---

### 20. Implement prototype-level cybersecurity

The project will consider:

- Client authentication
- TLS-protected MQTT communication
- MQTT ACLs
- WSS where applicable
- Command authorization
- Payload validation

These mechanisms are intended for the engineering prototype and will not be presented as production automotive cybersecurity certification.

---

### 21. Validate the complete BMS-oriented architecture

The final system will be validated as one connected architecture.

Validation will consider:

- SOC estimation accuracy
- EKF convergence
- Real-time response
- Drive-cycle response
- Regenerative braking
- Battery monitoring
- Fault detection
- Alarm response
- Cell imbalance detection
- Communication reliability
- Digital Twin synchronization
- Replay consistency
- Command-path behavior
- HMI performance
- Web monitoring
- Security controls

---

# 3. Scope

## 3.1. In Scope

### A. EV Driving Cycle and Operating Scenario

The project will use a driving-cycle-based source for realistic battery operating conditions.

**Driving Cycle → Vehicle Speed → Power Demand → Battery Current → Battery Pack**

The operating scenario will include:

- Acceleration
- Cruising
- Braking
- Idle
- Variable load
- Regenerative braking

---

### B. BMS Core

The **BMS Core** is the central scope of the project.

It will include:

**1. Measurement Processing**

- Voltage
- Current
- Temperature
- Cell voltage information

**2. State Estimation**

- SOC
- EKF
- Covariance
- Estimation uncertainty

**3. Battery Monitoring**

- Pack state
- Cell voltage limits
- Cell imbalance
- Temperature
- Current
- Voltage

**4. Fault Management**

- Over-voltage
- Under-voltage
- Over-temperature
- Overcurrent
- Cell imbalance
- Sensor faults
- Communication faults

**5. Alarm Management**

- Normal
- Warning
- Critical

---

### C. Battery Pack Model

The project focuses on a **355-V-class Li-ion EV battery pack** or equivalent representative model.

The battery representation will include:

- Pack voltage
- Pack current
- Temperature
- SOC
- Relevant internal/model states
- Selected cell-level information

The conceptual hierarchy is:

**Cell → Module → Pack → Vehicle**

The primary real-time EKF will use an Equivalent Pack Model.

---

### D. State Estimation

State estimation is a core BMS function.

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
- Noise testing
- Initial-condition testing

---

### E. Fault Injection and Abnormal Scenarios

Controlled fault injection will reproduce selected abnormal conditions.

These include:

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
- Delayed messages
- Missing messages
- Communication interruption

---

### F. Digital Twin

The Digital Twin will provide the software representation of the BMS-managed battery.

It will:

- Receive measurements
- Update the battery model
- Maintain the digital state
- Integrate EKF estimates
- Track uncertainty
- Represent selected cell/module/pack information
- Track faults and alarms
- Maintain synchronization with the data stream

The Digital Twin will support:

**Live Twin**

and

**Analytical Twin**

---

### G. IIoT / MQTT

MQTT will be the primary messaging protocol.

The architecture will include:

- MQTT Broker
- Publisher/Subscriber components
- BMS telemetry
- State-estimation messages
- Alarm/fault messages
- Command messages
- Acknowledgement messages
- Connection monitoring
- Reconnection handling

---

### H. Secure Industrial HMI

The HMI will provide operator-level BMS monitoring.

It will display:

- Voltage
- Current
- Temperature
- SOC
- SOC uncertainty
- Cell voltage information
- Cell imbalance
- BMS state
- EKF state
- Fault status
- Alarm status
- Communication status

---

### I. Web-Based Monitoring

The web dashboard will provide:

- Live BMS measurements
- SOC
- Estimation uncertainty
- Time-series plots
- Actual vs. estimated SOC
- Measured vs. predicted voltage
- BMS state
- Cell-level summary
- Alarm status
- Communication status
- Historical data
- Replay controls

---

### J. Replay System

The system will support recording and replaying BMS/simulation runs.

A recorded run can later be streamed again through the same BMS monitoring and Digital Twin pipeline.

---

### K. Command and Control Simulation

The web interface will support limited simulation commands.

The command path is:

**Web UI → Authentication → Validation → Authorization → MQTT → Simulator**

Telemetry and command traffic will remain logically separate.

---

### L. Security

Prototype-level security mechanisms include:

- Client Authentication
- TLS
- MQTT ACL
- WSS
- Command Authorization
- Payload Validation

---

### M. System Integration and Validation

The final system will integrate:

**Driving Cycle**

↓

**Vehicle/Power Model**

↓

**Battery Pack Model**

↓

**BMS Measurement Layer**

↓

**C++ EKF**

↓

**BMS State & Fault Management**

↓

**Digital Twin**

↓

**MQTT / IIoT**

↓

**Industrial HMI**

↓

**Web Dashboard**

---

# 3.2. Out of Scope

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
- Advanced fault diagnosis beyond defined prototype scenarios

These may be considered as future extensions after the BMS core architecture is stable.

---

# 4. Constraints

## 4.1. BMS and Battery Modeling Constraints

The BMS operates on a simplified representation of a real EV battery.

Battery behavior may vary with:

- SOC
- Temperature
- Current
- Aging
- Operating conditions

The Equivalent Pack Model used by the primary EKF will not reproduce every individual cell behavior.

The 355-V-class value represents a nominal voltage class and not a constant operating voltage.

---

## 4.2. EKF Constraints

EKF performance depends on:

- Battery model
- State initialization
- Initial covariance
- Process-noise covariance
- Measurement-noise covariance
- Parameter identification
- Measurement quality
- Model mismatch
- Numerical implementation

The EKF is not guaranteed to converge under every combination of poor initialization, severe noise, or incorrect model parameters.

---

## 4.3. BMS Fault Detection Constraints

Fault injection is intentionally artificial.

An injected sensor bias, temperature abnormality, or cell-voltage deviation does not reproduce every physical mechanism that causes a real battery fault.

The fault layer therefore evaluates:

- Detection logic
- Alarm generation
- Data handling
- Visualization
- Communication
- System response

rather than claiming physical validation of real battery failures.

---

## 4.4. Data Constraints

The project may use simulated data, experimental data, or a combination.

Data may contain:

- Sensor noise
- Missing samples
- Different sampling rates
- Timing inconsistencies
- Limited operating conditions
- Uncertain reference values

For quantitative SOC evaluation, a reliable reference SOC is required.

---

## 4.5. Communication Constraints

MQTT communication depends on network availability and Broker behavior.

Potential issues include:

- Latency
- Packet loss
- Connection loss
- Delayed messages
- Duplicate messages
- Out-of-order data
- Invalid payloads

---

## 4.6. Security Constraints

The security architecture is intended for an engineering prototype.

TLS, authentication, ACLs, WSS, command authorization, and payload validation do not constitute complete automotive cybersecurity certification.

---

## 4.7. HMI and Visualization Constraints

The BMS monitoring interface must remain readable as the number of monitored signals increases.

The visualization must balance:

**Information Density ↔ Operator Readability**

Abnormal conditions must be distinguishable without excessive decorative graphics or unnecessary animation.

---

## 4.8. Computational Constraints

The architecture combines:

- Battery model
- BMS measurement processing
- EKF
- Fault injection
- Fault management
- MQTT
- Digital Twin
- HMI
- Web dashboard
- Replay engine

The BMS estimator must therefore remain computationally lightweight enough for real-time execution.

---

## 4.9. Project-Level Constraint

This project is defined as a:

**Research / Engineering Prototype and Proof of Concept (PoC)**

The priority is to build a complete, testable, measurable, reproducible, and technically defensible **BMS-oriented system** before increasing architectural complexity.

---

# 5. Final Day 0 System Boundary

## Inputs

**Driving Cycle + Vehicle Parameters + Battery Parameters + Measurement Data + Simulation Commands**

---

## BMS Core Processing

**Vehicle/Power Model**

↓

**Battery Model**

↓

**Measurement Processing**

↓

**C++ EKF**

↓

**SOC / Battery State Estimation**

↓

**Fault Detection**

↓

**Alarm Management**

---

## Extended System Processing

**BMS Core**

↓

**Digital Twin**

↓

**MQTT / IIoT**

↓

**Industrial HMI + Web Dashboard**

---

## Outputs

- Estimated SOC
- Estimation uncertainty
- Battery state
- Cell monitoring information
- Fault status
- Alarm status
- BMS status
- Industrial HMI
- Web Dashboard
- Replay results
- Analytical results

---

# 6. Core Data Flow

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

**Voltage / Current / Temperature / Cell Data**

↓

**BMS Measurement Layer**

↓

**C++ EKF**

↓

**SOC + Model States + Covariance**

↓

**BMS State & Fault Management**

↓

**Digital Twin**

↓

**MQTT / IIoT**

↓

**Industrial HMI + Web Dashboard**

---

# 7. Command Flow

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

---

# 8. Demonstration Modes

## LIVE MODE

Real-time battery simulation, BMS estimation, fault monitoring, Digital Twin synchronization, and monitoring.

## REPLAY MODE

Reproduction of a previously recorded BMS/simulation scenario.

## ANALYTICAL MODE

Comparison of:

- Actual SOC vs. Estimated SOC
- Measured Voltage vs. Predicted Voltage
- Estimation Error
- EKF Uncertainty
- Battery response
- Fault response

---

# 9. Core Deliverable

> **A reproducible, real-time BMS-oriented Digital Twin prototype for a 355-V-class Li-ion EV battery pack that integrates realistic driving-cycle simulation, battery measurement processing, C++ EKF state estimation, SOC monitoring, fault detection, alarm management, Digital Twin synchronization, MQTT/IIoT communication, secure command handling, industrial HMI, web-based monitoring, and replay-based analysis into one end-to-end system.**