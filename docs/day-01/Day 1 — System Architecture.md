# Day 1 — System Architecture

## 1. Architecture Overview

The system is designed as a modular, real-time Digital Twin for a 355-V-class Li-ion EV battery pack.

The main goal of the architecture is to keep each part of the system focused on a specific responsibility while allowing all components to work together as one complete engineering system.

The system is divided into the following logical layers:

1. Vehicle & Drive-Cycle Layer
2. Battery Modeling & Simulation Layer
3. State Estimation Layer
4. Digital Twin & Fault Management Layer
5. IIoT Communication Layer
6. Application & Backend Layer
7. Visualization Layer
8. Data Storage & Replay Layer
9. Integration & Deployment Layer

The main data path is:

```text
Realistic Input
      ↓
Physical/System Simulation
      ↓
State Estimation
      ↓
Digital Twin
      ↓
IIoT Communication
      ↓
Backend
      ↓
HMI / Web Dashboard
```

The system also has a separate control path for sending commands back to the simulator:

```text
Web Dashboard
      ↓
Authentication
      ↓
Command Validation
      ↓
Authorization
      ↓
Backend
      ↓
MQTT
      ↓
Simulator
```

Keeping these two paths separate makes the system easier to understand, test, and secure.

---

# 2. High-Level System Architecture

```text
┌─────────────────────────────────────────────────────────────────────┐
│                    VEHICLE / INPUT LAYER                            │
│                                                                     │
│  Driving Cycle → Vehicle Speed → Power Demand → Battery Current     │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  MATLAB / SIMULINK LAYER                            │
│                                                                     │
│  Vehicle / Power Model                                              │
│          ↓                                                          │
│  Battery Pack Model                                                 │
│          ↓                                                          │
│  Measurements: V / I / T                                            │
│          ↓                                                          │
│  Fault Injection                                                    │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Measurements
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     C++ ESTIMATION LAYER                            │
│                                                                     │
│                         C++ + Eigen                                 │
│                                                                     │
│                  Extended Kalman Filter (EKF)                       │
│                                                                     │
│   State Prediction → Covariance → Measurement Update → Correction   │
│                                                                     │
│   Outputs: SOC / States / Covariance / Estimation Status            │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Estimated State + Measurements
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    DIGITAL TWIN LAYER                               │
│                                                                     │
│  Live Twin                                                         │
│  Analytical Twin                                                   │
│                                                                     │
│  Battery State + SOC + Temperature + Voltage + Current              │
│  Cell/Module/Pack Information + Uncertainty + Fault Status          │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │ Telemetry / State / Alarms
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       MQTT / IIoT LAYER                             │
│                                                                     │
│                        Mosquitto                                    │
│                                                                     │
│  Telemetry Topics                                                  │
│  Estimator Topics                                                  │
│  Alarm / Fault Topics                                              │
│  Command Topics                                                    │
│  Command ACK Topics                                                │
└───────────────┬─────────────────────────────┬───────────────────────┘
                │                             │
                ▼                             ▼
┌──────────────────────────────┐   ┌──────────────────────────────────┐
│      APPLICATION LAYER       │   │        INDUSTRIAL HMI            │
│                              │   │                                  │
│          FastAPI             │   │   Operator Monitoring             │
│                              │   │   Alarms / Trends / KPIs          │
│  Authentication              │   │   System Status                   │
│  Authorization               │   │   Battery Information             │
│  Command Validation          │   │                                  │
│  API                         │   │                                  │
└───────────────┬──────────────┘   └──────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       WEB INTERFACE                                 │
│                                                                     │
│                           React                                     │
│                                                                     │
│  Dashboard / Live Monitoring / Analytical Views / Replay            │
│  Trends / Alarms / System Status / Control Commands                 │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      DATA / STORAGE LAYER                            │
│                                                                     │
│                           SQLite                                    │
│                                                                     │
│  Telemetry / Events / Alarms / Runs / Replay Data / Metadata        │
└─────────────────────────────────────────────────────────────────────┘
```

---

# 3. Technology Responsibility Map

Each technology has a specific role in the system. The purpose of this separation is to avoid overlapping responsibilities and to make the interfaces between components clear.

| Technology | Responsibility |
|---|---|
| **MATLAB** | Mathematical Modeling & Analysis |
| **Simulink** | Plant / System Simulation |
| **C++ + Eigen** | Real-Time State Estimation / EKF |
| **Python** | Validation, Data Analysis & Visualization |
| **MQTT / Mosquitto** | IIoT Communication / Messaging |
| **FastAPI** | Application & Backend Layer |
| **React** | Web Interface / Dashboard |
| **SQLite** | Initial Data Storage |
| **Docker / Docker Compose** | Integration & Service Environment |
| **Git / GitHub** | Version Control & Collaboration |

These technologies are not being used interchangeably. Each one has a defined responsibility within the overall architecture.

---

# 4. Layer Responsibilities

## 4.1 Vehicle & Drive-Cycle Layer

This layer defines the operating conditions under which the battery is evaluated.

Instead of feeding the battery model with randomly generated current, the system starts with a driving cycle. Vehicle speed is then converted into power demand, which determines the battery current required by the simulated vehicle.

```text
Driving Cycle
      ↓
Vehicle Speed
      ↓
Power Demand
      ↓
Battery Current
```

The model should be able to represent common EV operating conditions such as:

- Acceleration
- Cruising
- Braking
- Idle
- Variable load
- Regenerative braking

This gives the battery model a realistic and repeatable operating scenario.

---

## 4.2 MATLAB / Simulink Layer

MATLAB and Simulink provide the main environment for battery modeling and system simulation. They have different roles within this layer.

### MATLAB

MATLAB is mainly used for:

- Mathematical modeling
- Parameter calculations
- Battery model analysis
- Model validation
- Offline analysis

### Simulink

Simulink is used for the actual system and plant simulation, including:

- Plant implementation
- Vehicle and power simulation
- Battery pack simulation
- Measurement generation
- Fault injection
- Real-time simulation flow

The main simulation path is:

```text
Drive Cycle
     ↓
Vehicle Model
     ↓
Power Demand
     ↓
Battery Model
     ↓
V / I / T Measurements
     ↓
Fault Injection
```

The output of this layer provides the measurements and operating conditions required by the estimation and Digital Twin layers.

---

# 5. Battery Model Architecture

The battery is represented using a hierarchical structure:

```text
Cell
  ↓
Module
  ↓
Pack
  ↓
Vehicle
```

This structure allows the Digital Twin to maintain information at different levels of the battery system.

For the first real-time EKF implementation, however, the estimator operates on an **Equivalent Pack Model**.

```text
                 Battery Pack
                     │
        ┌────────────┴────────────┐
        │                         │
 Equivalent Pack Model       Cell-Level Representation
        │                         │
        ▼                         ▼
      C++ EKF             Monitoring / Fault Scenarios
```

This approach keeps the real-time estimator manageable while still allowing the Digital Twin to retain cell-level information for monitoring and fault-related scenarios.

The cell-level representation is therefore not discarded; it simply does not have to be part of the initial real-time EKF state vector.

---

# 6. State Estimation Layer

The C++ estimation layer receives the battery measurements generated by Simulink.

The estimator is implemented using **C++ and Eigen**, with the **Extended Kalman Filter (EKF)** as the main estimation method.

```text
V / I / T
   │
   ▼
┌──────────────────────┐
│       C++ EKF        │
│                      │
│ State Prediction     │
│ Covariance Prediction│
│ Measurement Update   │
│ Gain Calculation     │
│ State Correction     │
└──────────┬───────────┘
           │
           ▼
     Estimated State
```

The main state to be estimated is:

```text
SOC
```

Other states depend on the selected equivalent-circuit battery model.

The EKF also provides covariance information. This gives the system an indication of estimation uncertainty rather than reporting SOC as a single value with no information about confidence.

---

# 7. Digital Twin Layer

The Digital Twin acts as the software representation of the battery system.

It brings together measurements from the simulation, estimates from the EKF, and the current operating and fault status of the battery.

The Digital Twin can contain:

- Simulation measurements
- EKF estimates
- Battery state
- Temperature
- Voltage
- Current
- Cell / module / pack information
- Estimation uncertainty
- Fault status
- Alarm status

The relationship between the main components is:

```text
Simulation
     │
     ├── Measurements
     │
     ▼
    EKF
     │
     ├── Estimated SOC
     ├── Model States
     └── Covariance
             │
             ▼
        Digital Twin
             │
             ├── Current State
             ├── Historical State
             ├── Fault State
             └── Alarm State
```

The Digital Twin therefore becomes the central point for representing the current and historical state of the simulated battery.

---

# 8. Digital Twin Operating Modes

The Digital Twin is designed to support three main operating modes.

## 8.1 Live Mode

Live Mode follows the battery simulation as it runs.

```text
Simulation
     ↓
EKF
     ↓
MQTT
     ↓
Digital Twin
     ↓
Monitoring
```

In this mode, the Digital Twin receives the latest measurements and estimates and updates its current state continuously.

---

## 8.2 Replay Mode

Replay Mode allows a previously recorded run to be played back through the same communication path.

```text
Recorded Run
     ↓
Replay Engine
     ↓
MQTT
     ↓
Digital Twin
     ↓
Monitoring
```

This is useful for:

- Demonstration
- Debugging
- Testing
- Result comparison
- HMI development
- Estimator evaluation

Replay is especially useful when testing the frontend or communication layer because the same scenario can be reproduced without running a new simulation every time.

---

## 8.3 Analytical Mode

Analytical Mode is used to compare measured, simulated, and estimated values.

For example:

```text
Actual SOC
     vs.
Estimated SOC
```

and:

```text
Measured Voltage
     vs.
Predicted Voltage
```

The analysis can include:

- Estimation error
- RMSE
- MAE
- Maximum error
- Convergence time
- EKF uncertainty
- Response to defined events

This makes it possible to evaluate the estimator using measurable results rather than relying only on visual inspection.

---

# 9. MQTT / IIoT Communication Layer

**Mosquitto** is used as the MQTT Broker.

The MQTT layer provides the communication path between the different services without coupling those services directly to one another.

The basic communication model is:

```text
Publisher
    │
    ▼
 MQTT Broker
    │
    ├──────────────► Subscriber
    │
    ├──────────────► Backend
    │
    ├──────────────► HMI
    │
    └──────────────► Other Services
```

Telemetry and control traffic are kept separate so that monitoring data and system commands are not mixed together.

Initial topic groups can include:

```text
telemetry
estimator
alarm
fault
command
command/ack
```

The exact topic namespace and message payload structure will be defined during the implementation stage.

---

# 10. Application / Backend Layer

**FastAPI** provides the application and backend layer.

It acts as the main interface between the Web application and the lower-level system services.

Its responsibilities include:

- API endpoints
- Authentication
- Authorization
- Command validation
- Data access
- MQTT communication
- Dashboard data services
- System status
- Replay control
- Simulation control

The basic structure is:

```text
React
  │
  ▼
FastAPI
  │
  ├── Authentication
  ├── Authorization
  ├── Validation
  ├── Data Access
  └── MQTT Interface
          │
          ▼
       Mosquitto
```

This keeps the React application independent of the MQTT broker and the simulator.

---

# 11. Command Architecture

Commands follow a controlled path from the Web interface to the simulator.

```text
React Web UI
      ↓
FastAPI
      ↓
Authentication
      ↓
Command Validation
      ↓
Authorization
      ↓
MQTT
      ↓
Simulator
      ↓
Command ACK
      ↓
MQTT
      ↓
FastAPI
      ↓
React
```

Examples of system commands include:

```text
START_SIMULATION
STOP_SIMULATION
RESET
SELECT_DRIVE_CYCLE
START_REPLAY
STOP_REPLAY
```

The Web interface does not communicate directly with the simulator. Every command passes through the Backend, where it can be authenticated, validated, and authorized before it reaches the simulation layer.

---

# 12. Web Interface Layer

**React** provides the Web-based monitoring and control interface.

The Dashboard receives processed system information through the Backend rather than accessing the lower-level components directly.

The interface can be organized into several main sections.

### System Overview

- Pack voltage
- Pack current
- Temperature
- SOC
- Estimation uncertainty
- System state

### Battery Monitoring

- Minimum cell voltage
- Maximum cell voltage
- Cell voltage spread
- Cell imbalance

### Estimator Monitoring

- Actual vs. estimated SOC
- Measured vs. predicted voltage
- Estimation error
- EKF uncertainty

### Alarm Monitoring

- Active alarms
- Severity
- Alarm source
- Timestamp
- Current condition

### System Control

- Start simulation
- Stop simulation
- Reset
- Select drive cycle
- Start replay
- Stop replay

The Dashboard should focus on useful system information rather than simply displaying as many values as possible.

---

# 13. Data Storage Layer

**SQLite** is used as the initial persistent storage solution.

The database can store information such as:

```text
Telemetry
     ↓
State Estimates
     ↓
Alarms
     ↓
Fault Events
     ↓
Simulation Runs
     ↓
Replay Metadata
```

SQLite keeps the storage layer simple during the prototype stage while still providing enough functionality for recorded runs, analysis, and replay.

To make experiments reproducible, stored data should be linked to information such as:

- Run ID
- Scenario
- Drive cycle
- Timestamp
- Configuration
- Relevant estimator parameters

This makes it possible to identify exactly which configuration produced a particular dataset.

---

# 14. Python Validation Layer

Python is kept outside the real-time estimation path.

Its main role is to process recorded data and evaluate the performance of the system.

Python will be used for:

- Validation
- Data analysis
- Visualization
- Performance evaluation
- Experiment comparison

The general workflow is:

```text
Recorded Data
      ↓
Python
      ↓
Data Processing
      ↓
Metrics
      ↓
Visualization
      ↓
Validation Report
```

Typical evaluation metrics include:

```text
MAE
RMSE
Maximum Absolute Error
Convergence Time
```

Keeping Python outside the real-time path allows the C++ EKF to remain an independent estimator while Python handles the analysis and evaluation work.

---

# 15. Industrial HMI

The Industrial HMI is intended for operator-focused monitoring.

Unlike a general-purpose Web Dashboard, the HMI should make important system conditions easy to recognize, especially when something goes wrong.

The HMI should focus on:

- Clear information hierarchy
- Low visual clutter
- Abnormal-condition visibility
- Alarm prioritization
- Trend visualization
- System status
- Battery state
- Communication status

The goal is not to create a decorative interface. The HMI should present the information an operator needs to understand the system state quickly.

---

# 16. Security Architecture

Security is mainly applied to the communication and command paths.

```text
                    ┌──────────────┐
                    │   React UI   │
                    └──────┬───────┘
                           │
                          WSS
                           │
                           ▼
                    ┌──────────────┐
                    │   FastAPI    │
                    │              │
                    │ Authentication│
                    │ Authorization │
                    │ Validation    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │    MQTT      │
                    │    TLS       │
                    │    ACL       │
                    └──────┬───────┘
                           │
                           ▼
                       Simulator
```

The prototype will use security mechanisms such as:

- Client authentication
- TLS
- MQTT ACL
- WSS where applicable
- Command authorization
- Payload validation

These mechanisms are intended to provide a reasonable security baseline for the prototype. They are not intended to represent production-level automotive cybersecurity certification.

---

# 17. End-to-End Data Flow

The complete telemetry path through the system is:

```text
Driving Cycle
      ↓
Vehicle Speed
      ↓
Power Demand
      ↓
Battery Current
      ↓
Battery Pack Model
      ↓
V / I / T
      ↓
Fault Injection
      ↓
C++ EKF
      ↓
SOC / States / Covariance
      ↓
Digital Twin
      ↓
MQTT / Mosquitto
      ↓
FastAPI
      ↓
React Dashboard
```

The Industrial HMI can subscribe to the relevant MQTT data streams for operator monitoring.

This gives the project a clear end-to-end path from the driving scenario all the way to the final user interface.

---

# 18. End-to-End Command Flow

The command path works in the opposite direction:

```text
React
  ↓
FastAPI
  ↓
Authentication
  ↓
Validation
  ↓
Authorization
  ↓
MQTT
  ↓
Simulator
  ↓
Command ACK
  ↓
MQTT
  ↓
FastAPI
  ↓
React
```

This creates a clear separation between:

```text
DATA FLOW
```

and:

```text
CONTROL FLOW
```

Keeping these paths separate makes the architecture easier to maintain and gives the command path its own validation and security controls.

---

# 19. Deployment Architecture

**Docker and Docker Compose** provide the integration environment for the software services.

A simplified deployment structure is:

```text
┌────────────────────────────────────────────────────────────┐
│                    Docker Compose                          │
│                                                            │
│  ┌─────────────┐     ┌──────────────┐                     │
│  │   FastAPI   │────►│   Mosquitto  │                     │
│  └──────┬──────┘     └──────┬───────┘                     │
│         │                    │                             │
│         ▼                    ▼                             │
│  ┌─────────────┐      MQTT Clients                        │
│  │    SQLite   │                                             │
│  └─────────────┘                                             │
│                                                            │
│  ┌─────────────┐                                           │
│  │    React    │                                           │
│  └─────────────┘                                           │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

During the initial development stages, MATLAB/Simulink and the C++ EKF can run as external development processes.

They can later be integrated into the deployment environment where this provides a real technical benefit.

The purpose of Docker is to make service integration and the development environment reproducible. It is not necessary to force every engineering tool into a container simply for the sake of containerization.

---

# 20. System Architecture Principle

The architecture is built around modularity and clear interfaces.

```text
             ┌───────────────────┐
             │ Drive Cycle       │
             └─────────┬─────────┘
                       ↓
             ┌───────────────────┐
             │ MATLAB / Simulink │
             └─────────┬─────────┘
                       ↓
             ┌───────────────────┐
             │ C++ / Eigen EKF   │
             └─────────┬─────────┘
                       ↓
             ┌───────────────────┐
             │   Digital Twin    │
             └─────────┬─────────┘
                       ↓
             ┌───────────────────┐
             │ MQTT / Mosquitto  │
             └───────┬───┬───────┘
                     │   │
             ┌───────┘   └────────┐
             ↓                    ↓
       ┌────────────┐       ┌────────────┐
       │ Industrial │       │  FastAPI   │
       │    HMI     │       └─────┬──────┘
       └────────────┘             ↓
                              ┌────────────┐
                              │   React    │
                              └─────┬──────┘
                                    ↓
                              ┌────────────┐
                              │   SQLite   │
                              └────────────┘
```

Each major component should have a defined responsibility and a clear interface with the components around it.

This allows individual parts of the system to be developed, tested, replaced, or improved without forcing a redesign of the entire project.

---

# 21. Architectural Design Goals

The architecture is designed around the following goals:

- Modularity
- Real-time operation
- Reproducibility
- Testability
- Clear separation of responsibilities
- Clear data flow
- Controlled command flow
- Fault injection capability
- Estimator validation
- Digital Twin synchronization
- IIoT connectivity
- Secure communication
- Web-based monitoring
- Replay capability
- Future extensibility

The priority is to build a system that works, can be measured, and can be tested.

Additional complexity should only be introduced when it solves a real engineering problem or provides a clear benefit to the project.

---

# 22. Architecture-to-Technology Mapping

| System Layer | Main Technology | Primary Role |
|---|---|---|
| Vehicle / Drive Cycle | MATLAB / Simulink | Operating Scenario |
| Battery Model | MATLAB / Simulink | Plant Simulation |
| Fault Injection | Simulink / MATLAB | Abnormal Scenarios |
| State Estimation | C++ / Eigen | Real-Time EKF |
| Validation | Python | Metrics and Analysis |
| Digital Twin | Application / System Logic | Digital State Synchronization |
| IIoT | MQTT / Mosquitto | Messaging |
| Backend | FastAPI | API, Security and Application Logic |
| Web UI | React | Monitoring and Control |
| Industrial HMI | HMI Layer | Operator Monitoring |
| Storage | SQLite | Persistent Prototype Data |
| Integration | Docker Compose | Service Orchestration |
| Version Control | Git / GitHub | Source and Configuration Management |

---

# 23. Architectural Boundary

The main system boundary can be summarized as follows:

```text
                    SYSTEM BOUNDARY
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  INPUT                                                      │
│  Driving Cycle / Parameters / Commands                      │
│                         ↓                                   │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ MATLAB / Simulink                                    │  │
│  │ Vehicle + Battery + Fault Injection                  │  │
│  └───────────────────────┬──────────────────────────────┘  │
│                          ↓                                  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ C++ / Eigen EKF                                      │  │
│  └───────────────────────┬──────────────────────────────┘  │
│                          ↓                                  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Digital Twin + Alarm Management                      │  │
│  └───────────────────────┬──────────────────────────────┘  │
│                          ↓                                  │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ MQTT / Mosquitto                                     │  │
│  └───────────────┬───────────────────┬──────────────────┘  │
│                  ↓                   ↓                     │
│             Industrial HMI       FastAPI                  │
│                                      ↓                     │
│                                    React                   │
│                                      ↓                     │
│                                    SQLite                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

The main inputs to the system are driving cycles, simulation parameters, and user commands.

The system outputs battery measurements, estimated states, fault and alarm information, historical data, and monitoring information for the HMI and Web Dashboard.

---

# 24. Final Architecture Statement

The project uses a layered and modular Digital Twin architecture for a 355-V-class Li-ion EV battery pack.

MATLAB/Simulink provides the vehicle and battery simulation environment, while C++/Eigen handles real-time EKF state estimation. Python is used for offline validation, data analysis, and visualization. MQTT/Mosquitto provides the IIoT communication layer, FastAPI handles the application, backend, and security logic, and React provides the Web-based monitoring and control interface. SQLite is used for initial persistent storage, while Docker Compose provides the integration environment for the software services.

Together, these components form a single end-to-end engineering system while keeping simulation, estimation, communication, application logic, visualization, and storage clearly separated.

The architecture is designed so that each major component has a defined responsibility and interface. This makes the system easier to develop and test and also leaves room for future improvements without requiring the entire architecture to be redesigned.

The final objective is not simply to connect a collection of technologies. The objective is to build a working Digital Twin system in which battery behavior can be simulated, internal states can be estimated, faults can be introduced, data can be communicated and stored, and the resulting system state can be monitored and evaluated through a unified interface.