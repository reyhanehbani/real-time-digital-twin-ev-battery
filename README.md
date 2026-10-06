# Real-Time Digital Twin of a 355-V-Class Li-ion EV Battery Pack

## Overview

This project focuses on the design and implementation of a **BMS-oriented real-time Digital Twin** for a 355-V-class Lithium-ion Electric Vehicle (EV) battery pack.

The system is designed around the core monitoring, estimation, diagnostics, and communication requirements of a **Battery Management System (BMS)** while using Digital Twin concepts to provide a dynamic software representation of the battery pack.

The architecture combines mathematical battery modeling, simulation, BMS-oriented state estimation, validation and data analysis, IIoT communication, fault and condition monitoring, and web-based visualization within a unified software architecture.

The primary objective is to develop a modular and extensible digital representation of an EV battery pack that can continuously process battery operational data, estimate internal battery states, validate estimation performance, support BMS-oriented monitoring and diagnostics, and expose meaningful information through a fully web-based monitoring environment.

The Digital Twin therefore acts as a **model-based software representation and supervisory layer around the battery/BMS domain**, rather than replacing the safety-critical functions of a physical BMS.

The project is entirely **software-based**. No physical BMS hardware, embedded controller, PLC, industrial HMI panel, sensors, or other dedicated hardware is required or implemented as part of the project.

---

## Project Objectives

The project aims to:

* Develop a mathematical and computational model of an EV Li-ion battery pack suitable for BMS-oriented applications.

* Develop the battery simulation environment using **MATLAB/Simulink**.

* Define and implement a BMS-oriented battery monitoring architecture.

* Implement real-time State of Charge (SoC) estimation using an Extended Kalman Filter (EKF).

* Develop the core estimation components in **C++ using Eigen**.

* Process and monitor key BMS variables including voltage, current, temperature, SoC, power, and estimated internal states.

* Establish the foundations for BMS-oriented state monitoring and fault/condition detection.

* Establish IIoT communication using the **MQTT protocol**.

* Implement MQTT communication and data handling using **Python and Paho MQTT**.

* Perform validation, numerical analysis, data processing, visualization, and result analysis using **Python**.

* Generate structured JSON data for communication, testing, and integration.

* Develop a fully web-based monitoring interface.

* Develop the web application and monitoring dashboard using modern web technologies.

* Design the web-based HMI and dashboard interfaces using **Figma** for wireframing and interface planning.

* Establish a structured data pipeline between the battery simulation, BMS-oriented estimation layer, validation layer, communication layer, and web visualization layer.

* Validate the performance of the Digital Twin and its estimation components against reference data and defined battery operating scenarios.

---

## System Concept

The conceptual architecture of the system is:

```text
                    EV Battery Pack Model
                         MATLAB/Simulink
                              │
                              │
                 Voltage / Current / Temperature
                              │
                              ▼
                    ┌──────────────────┐
                    │ Data Acquisition │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Battery Model  │
                    │  MATLAB/Simulink │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   EKF Estimator  │
                    │   C++ / Eigen    │
                    │  SoC / States    │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ BMS State & Fault│
                    │    Monitoring    │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Python Validation│
                    │ Data Analysis &  │
                    │  JSON Generation │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   MQTT / IIoT    │
                    │  Python / Paho   │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Web Application  │
                    │  & Backend Layer │
                    └────────┬─────────┘
                             │
                       ┌─────┴─────┐
                       ▼           ▼
                 Web-Based HMI   Web Dashboard
```

The architecture is modular so that individual components can be tested, replaced, or extended without redesigning the entire system.

The BMS domain provides the primary engineering context for battery monitoring, state estimation, and condition monitoring, while the Digital Twin provides the model-based representation, simulation, data integration, validation, and supervisory monitoring capabilities.

All HMI and visualization functions are implemented as **web-based software interfaces**. No dedicated industrial HMI hardware is part of the system.

---

## Technology Responsibility Map

Each technology has a defined responsibility within the project.

| Technology     | Responsibility                                                                                         |
| -------------- | ------------------------------------------------------------------------------------------------------ |
| **MATLAB**     | Mathematical modeling, battery equations, model development, numerical modeling                        |
| **Simulink**   | Battery simulation, dynamic system modeling, drive-cycle execution, simulation environment             |
| **C++**        | Core real-time estimation implementation                                                               |
| **Eigen**      | Linear algebra and numerical operations inside the C++ estimator                                       |
| **EKF**        | BMS-oriented SoC and internal state estimation                                                         |
| **Python**     | Validation, data analysis, result processing, test automation, data generation, JSON generation        |
| **NumPy**      | Numerical computation and array-based data processing in Python                                        |
| **Pandas**     | Dataset processing, organization, filtering, comparison, and analysis                                  |
| **Matplotlib** | Engineering plots, validation plots, estimation performance visualization                              |
| **SciPy**      | Scientific computing, numerical analysis, signal/data processing, and supporting validation algorithms |
| **Paho MQTT**  | MQTT publishing/subscription and IIoT communication from Python                                        |
| **MQTT**       | Messaging and decoupled data communication between software components                                 |
| **JSON**       | Structured data exchange between simulation, validation, communication, backend, and web layers        |
| **FastAPI**    | Application/backend API layer                                                                          |
| **React**      | Web-based monitoring dashboard and HMI interface                                                       |
| **Figma**      | Wireframing, UI planning, and interface design                                                         |
| **Git**        | Version control                                                                                        |
| **GitHub**     | Source-code repository and project collaboration                                                       |
| **Markdown**   | Technical documentation                                                                                |

The technologies are intentionally separated by responsibility to maintain modularity and traceability throughout the project.

---

## Battery System

The target system represents a 355-V-class Li-ion EV battery pack.

The battery system is represented through a **software-based mathematical and simulation model** rather than physical battery hardware.

The Digital Twin is intended to represent battery variables relevant to BMS operation, including:

* Pack voltage

* Battery current

* Cell or pack temperature

* State of Charge (SoC)

* Estimated internal states

* Power demand

* Relevant battery model parameters

* Battery operating condition

* BMS status information

* Fault and warning information

The final set of monitored variables will be defined during the system requirements and architecture phases.

Where appropriate, the architecture may distinguish between **pack-level measurements** and **cell-level measurements**, allowing the system to evolve toward more detailed BMS-oriented monitoring without changing the overall architecture.

---

## BMS-Oriented Digital Twin

The Digital Twin is designed around the operational requirements of a Battery Management System.

The BMS-oriented responsibilities represented within the Digital Twin include:

```text
Battery Modeling & Simulation
          │
          ▼
Battery Monitoring
          │
          ├── Voltage Monitoring
          ├── Current Monitoring
          ├── Temperature Monitoring
          │
          ▼
Battery State Estimation
          │
          ├── SoC Estimation
          ├── Internal State Estimation
          └── Model-Based Estimation
          │
          ▼
Battery Condition Monitoring
          │
          ├── Operating Limits
          ├── Warning Conditions
          └── Fault Indicators
          │
          ▼
Validation & Analysis
          │
          ├── Estimation Accuracy
          ├── Model Performance
          └── Test Scenarios
          │
          ▼
Communication & Visualization
          │
          ├── MQTT / IIoT
          ├── Web-Based HMI
          └── Web Dashboard
```

The Digital Twin does not replace the safety-critical protection mechanisms of a physical BMS.

Instead, it provides a software-based representation of the battery system that can support:

* BMS state estimation

* Battery condition monitoring

* Model-based diagnostics

* Data visualization

* Historical analysis

* Validation of BMS-related algorithms

* Digital representation of battery operating states

* Simulation-based testing

* Algorithm development

This separation allows the project to remain technically aligned with BMS engineering while preserving the broader Digital Twin architecture.

---

## Battery Modeling & Simulation

The mathematical and dynamic battery model is developed using **MATLAB and Simulink**.

MATLAB is responsible for the mathematical formulation and numerical implementation of the battery model, while Simulink provides the dynamic simulation environment.

The simulation layer is responsible for generating battery behavior under defined operating conditions and drive-cycle scenarios.

The simulation environment may provide:

* Battery voltage

* Battery current

* Temperature

* State of Charge

* Internal model states

* Power

* Model parameters

* Drive-cycle response

These outputs provide the reference and measurement data required by the downstream estimation and validation layers.

The battery simulation is therefore the primary **virtual plant** of the Digital Twin.

---

## State Estimation

A central component of the project is real-time battery state estimation within the BMS-oriented architecture.

An Extended Kalman Filter (EKF) will be investigated and implemented for estimating battery states that cannot be directly measured.

The estimator will use available battery measurements and the mathematical battery model to recursively estimate the internal state of the battery.

The general estimation loop is:

```text
Measurements
     │
     ▼
Prediction
     │
     ▼
State Estimate
     │
     ▼
Measurement Update
     │
     ▼
Updated State
```

The EKF core is implemented in **C++**.

**Eigen** is used as the primary C++ linear algebra library for:

* Matrix operations

* Vector operations

* Covariance calculations

* Jacobian-related computations

* Numerical operations required by the EKF

The estimator provides BMS-oriented state information that can be passed to the monitoring, validation, communication, and visualization layers.

The implementation and validation of the EKF will be documented separately within the project documentation.

---

## Validation & Data Analysis

All validation and engineering data analysis are performed using **Python**.

Python is responsible for:

* Reference data processing

* Simulation result processing

* EKF output analysis

* Estimation error calculation

* Statistical analysis

* Test scenario evaluation

* Performance comparison

* Result visualization

* Automated validation workflows

* JSON data generation

The primary Python libraries are:

### NumPy

Used for numerical computation, vectorized operations, matrix manipulation, and numerical processing.

### Pandas

Used for structured dataset processing, time-series analysis, filtering, comparison, and data management.

### Matplotlib

Used for engineering visualization, including:

* SoC comparison plots

* Voltage/current plots

* Estimation error plots

* Temperature plots

* Model-vs-reference comparisons

* Validation results

### SciPy

Used for scientific and numerical computing, including supporting numerical analysis, signal processing, interpolation, optimization, and other validation-related operations where required.

The Python validation layer is intentionally separated from the C++ estimator so that estimator performance can be independently evaluated against reference data.

---

## JSON Data Generation

JSON is used as a structured data representation between software components.

Python is responsible for generating, processing, and validating JSON-based data where required.

JSON may be used for:

* Battery state data

* Estimated state data

* Test scenarios

* Configuration data

* Validation outputs

* MQTT payloads

* Backend/API communication

This creates a standardized data representation between the computational, communication, backend, and visualization layers.

---

## Real-Time Data Flow

The intended BMS-oriented data pipeline is:

```text
MATLAB / Simulink
Battery Simulation
       │
       ▼
Battery Measurements
       │
       ▼
C++ / Eigen
EKF Estimator
       │
       ▼
BMS State & Condition Layer
       │
       ├───────────────┐
       │               │
       ▼               ▼
Python Validation   JSON Data
       │               │
       └───────┬───────┘
               ▼
        Python / Paho MQTT
               │
               ▼
           MQTT Broker
               │
               ▼
       FastAPI Backend
               │
               ▼
       React Web Application
               │
        ┌──────┴──────┐
        ▼             ▼
 Web-Based HMI   Web Dashboard
```

The data flow separates:

1. Battery simulation
2. Data acquisition
3. State estimation
4. BMS-oriented state and condition monitoring
5. Validation and analysis
6. Structured data generation
7. MQTT communication
8. Backend processing
9. Web-based visualization

This architecture allows the estimation and visualization layers to remain decoupled from the underlying battery simulation.

---

## IIoT Communication

The IIoT communication layer is based on **MQTT**.

Python and **Paho MQTT** are used to implement MQTT publishing and subscription.

The MQTT layer is responsible for decoupling the computational components from the application and visualization layers.

The communication architecture may include topics for:

```text
battery/
├── voltage
├── current
├── temperature
├── soc
├── power
├── estimated_state
├── status
├── warning
└── fault
```

The final topic hierarchy and message schema will be defined during the communication architecture phase.

JSON is used as the primary structured payload format where appropriate.

---

## Web-Based HMI & Dashboard

The HMI and monitoring environment are **entirely web-based**.

There is no dedicated industrial HMI panel or hardware HMI device in the project.

The web application provides the software equivalent of an industrial monitoring interface.

The web layer is responsible for:

* Real-time battery monitoring

* SoC visualization

* Voltage monitoring

* Current monitoring

* Temperature monitoring

* Power monitoring

* BMS state display

* Warning and fault visualization

* Historical data visualization

* System status

* Digital Twin monitoring

The planned web architecture uses:

* **React** for the frontend and web-based HMI/dashboard.

* **FastAPI** for the application/backend API layer.

* **MQTT** for real-time messaging between the computational/communication layers and the application layer.

The web interface is therefore both the **HMI** and the primary visualization environment of the project.

---

## UI/UX Design

The web-based HMI and dashboard are planned and designed using **Figma** before implementation.

Figma is used for:

* Wireframing

* UI layout planning

* Dashboard structure

* HMI screen design

* Component planning

* User interaction design

* Visual hierarchy

The Figma designs are then translated into the React-based web application.

The design process therefore follows:

```text
Requirements
     │
     ▼
Figma Wireframe
     │
     ▼
UI / UX Design
     │
     ▼
React Implementation
     │
     ▼
FastAPI Integration
     │
     ▼
Real-Time MQTT Data
     │
     ▼
Web-Based BMS HMI
```

---

## Digital Twin Concept

The Digital Twin is not limited to a static battery model.

The intended system continuously connects:

```text
Virtual Battery System
       │
       ↕
MATLAB / Simulink
       │
       ↕
Battery Model
       │
       ↕
C++ / Eigen EKF
       │
       ↕
BMS State Estimation
       │
       ↕
Condition / Fault Monitoring
       │
       ↕
Python Validation
       │
       ↕
MQTT / IIoT
       │
       ↕
FastAPI Backend
       │
       ↕
React Web HMI
```

This enables the Digital Twin to represent the dynamic behavior of the battery system and provide continuously updated estimated states.

The BMS-oriented architecture provides the engineering context for battery monitoring and state estimation, while the Digital Twin provides the computational representation and integration layer.

This approach also allows the same architecture to support:

* Battery simulation

* Algorithm development

* BMS state estimation

* Validation

* Data analysis

* Real-time monitoring

* Fault/condition visualization

* Future integration with physical battery/BMS data

---

## Project Structure

```text
real-time-digital-twin-ev-battery/

│
├── README.md
├── .gitignore
│
├── docs/
│   └── day-00/
│       └── project-definition.md
│
├── src/
│
├── models/
│
├── tests/
│
├── dashboard/
│
├── data/
│
├── config/
│
├── scripts/
│
└── results/
```

### Directory Description

* `docs/` — Project documentation, BMS architecture, engineering decisions, requirements, and validation documentation.

* `src/` — Main source code including C++ estimation, monitoring, diagnostics, and communication components.

* `models/` — MATLAB/Simulink battery and mathematical models used by the Digital Twin.

* `tests/` — Unit, integration, estimator, simulation, and validation tests.

* `dashboard/` — React-based web monitoring interface and web-based HMI.

* `data/` — Simulation, reference, validation, and processed datasets.

* `config/` — System, battery, estimator, communication, and application configuration.

* `scripts/` — Python validation, data-processing, JSON-generation, and automation scripts.

* `results/` — Experimental, simulation, estimation, and validation results.

---

## Development Methodology

The project will be developed incrementally through defined engineering phases.

### Phase 0 — Project Definition

* Problem Statement

* BMS-Oriented Problem Definition

* Objectives

* Scope

* Constraints

* Software-only system definition

### Phase 1 — System Requirements

* Functional requirements

* Non-functional requirements

* BMS monitoring requirements

* State estimation requirements

* Performance requirements

* Communication requirements

* Web HMI requirements

* Security requirements

### Phase 2 — System Architecture

* Overall architecture

* BMS-oriented system architecture

* Data flow

* Component interfaces

* Communication architecture

* Monitoring and diagnostic interfaces

* Software integration architecture

### Phase 3 — Battery Modeling

* Battery model selection

* Mathematical formulation

* Parameter definition

* MATLAB implementation

* Simulink implementation

* Drive-cycle simulation

* Model validation

* BMS-oriented model requirements

### Phase 4 — State Estimation

* EKF formulation

* State-space representation

* BMS state estimation requirements

* C++ implementation

* Eigen integration

* Estimator validation

### Phase 5 — IIoT Communication

* MQTT architecture

* BMS data topic structure

* JSON message format

* Paho MQTT implementation

* Data publishing and subscription

* Battery state and condition data transmission

### Phase 6 — HMI & Web Monitoring

* Figma wireframes

* UI/UX design

* Web-based HMI

* React frontend

* FastAPI backend

* BMS monitoring interface

* Real-time visualization

* Battery state monitoring

* Alarm and status monitoring

### Phase 7 — Integration

* End-to-end data flow

* BMS-oriented component integration

* MATLAB/Simulink integration

* C++ estimator integration

* Python validation integration

* MQTT integration

* FastAPI integration

* React integration

* Real-time testing

* Error handling

### Phase 8 — Validation & Documentation

* Test scenarios

* Simulation/reference comparison

* Estimation accuracy

* BMS state monitoring performance

* Model performance

* System performance

* Python-based data analysis

* Results visualization

* Results analysis

* Final technical documentation

---

## Current Status

**Project Phase:** Day 0 — Project Definition

### Completed

* Initial project definition

* BMS-oriented project definition

* Repository initialization

* Git version control

* GitHub repository

* Initial project structure

* Project documentation structure

### In Progress

* System requirements

* BMS-oriented system architecture

* Detailed architecture

* Battery model definition

* MATLAB/Simulink modeling strategy

* EKF design

* Technology responsibility definition

### Planned

* MATLAB/Simulink battery simulation

* C++ / Eigen EKF implementation

* BMS-oriented state monitoring

* Python validation pipeline

* JSON data generation

* MQTT / Paho MQTT communication

* FastAPI backend

* Figma UI/UX design

* React web-based HMI

* Web-based monitoring dashboard

* System integration

* Validation

---

## Engineering Principles

The project follows several principles:

1. **Modularity** — Each major subsystem should have a clear responsibility and interface.

2. **BMS Orientation** — Battery monitoring, state estimation, condition monitoring, and diagnostics should be designed around realistic BMS engineering requirements.

3. **Software-Only Architecture** — The project is implemented as a software-based Digital Twin and does not require or implement dedicated physical BMS, PLC, HMI, sensor, or embedded hardware.

4. **Technology Separation** — MATLAB/Simulink, C++, Python, MQTT, backend, and frontend technologies should each have clearly defined responsibilities.

5. **Traceability** — Engineering decisions should be documented and connected to requirements.

6. **Testability** — Components should be independently testable whenever possible.

7. **Reproducibility** — Simulation, validation, and analysis results should be reproducible from documented configurations and datasets.

8. **Security by Design** — Communication and monitoring components should consider authentication, authorization, and secure data handling.

9. **Separation of Concerns** — Battery modeling, state estimation, validation, communication, backend processing, and visualization should remain modular and independently maintainable.

10. **Incremental Development** — The system should be built and validated in controlled stages rather than as a single monolithic implementation.

11. **Model-Based Engineering** — Battery models and estimation algorithms should provide the computational foundation for the Digital Twin and BMS-oriented functions.

12. **Independent Validation** — The C++ estimation layer should be validated independently using Python-based numerical analysis and reference data.

---

## Project Status

This repository represents an active engineering project.

Features described as planned or in progress have not necessarily been implemented or validated yet.

The project currently represents a **BMS-oriented software Digital Twin architecture**, not a production-ready safety-critical Battery Management System.

The system is intentionally hardware-independent and is currently focused on:

* Battery modeling and simulation

* BMS-oriented state estimation

* Software-based monitoring

* Validation and data analysis

* IIoT communication

* Web-based HMI

* Digital Twin integration

---

## License

License information will be added after the project's distribution and usage requirements are defined.
