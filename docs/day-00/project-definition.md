# Day 0 — Project Definition

## 1. Project Overview

I am developing a **BMS-oriented real-time Digital Twin of a 355-V-class Lithium-ion EV battery pack**.

The main idea of this project is to combine two closely related engineering concepts:

* **Battery Management System (BMS)** engineering, especially battery monitoring, state estimation, condition monitoring, and diagnostics.
* **Digital Twin** concepts, which provide a dynamic software-based representation of the battery system and connect modeling, estimation, validation, communication, and visualization into one integrated environment.

The project is designed as a **software-based Digital Twin** around the EV battery and BMS domain. The goal is not to build a physical BMS. Instead, I want to create a computational representation of the battery that can simulate its behavior, process battery measurements, estimate internal states, monitor operating conditions, validate the results, and make the resulting information available through a web-based monitoring system.

The overall system combines:

* Mathematical battery modeling
* MATLAB/Simulink simulation
* BMS-oriented state estimation
* Extended Kalman Filter (EKF)
* C++ and Eigen for the estimator
* Python-based validation and data analysis
* JSON-based data exchange
* MQTT-based IIoT communication
* Fault and condition monitoring
* FastAPI backend
* React web application
* Web-based HMI and dashboard
* Figma-based UI/UX planning
* Git and GitHub for version control and project management

The final system is intended to behave as an integrated software environment rather than a collection of unrelated tools.

---

## 2. Problem Definition

An EV battery pack is a dynamic system whose internal condition cannot always be directly measured.

Some battery variables, such as terminal voltage, current, and temperature, can be obtained as measurements. However, important internal states such as **State of Charge (SoC)** and other model states need to be estimated using mathematical models and available measurements.

This creates a central BMS engineering problem:

> How can I build a software system that continuously represents the battery, processes its operating data, estimates important internal states, evaluates its condition, validates the estimation results, and presents the information in a practical monitoring environment?

A conventional mathematical battery model by itself is not enough for this purpose. It can represent battery behavior, but the complete project needs a larger system around the model.

For this reason, I am treating the battery model as the **virtual plant** and building the remaining BMS-oriented and Digital Twin functions around it.

The intended chain is:

```text
Battery Model
     ↓
Battery Measurements
     ↓
State Estimation
     ↓
BMS State & Condition Monitoring
     ↓
Validation & Analysis
     ↓
Communication
     ↓
Backend
     ↓
Web-Based HMI / Dashboard
```

This gives the project a clear engineering purpose: the Digital Twin should represent the battery dynamically and provide meaningful BMS-oriented information rather than only generating simulation plots.

---

## 3. Why This Project Is BMS-Oriented

The main engineering context of the project is the **Battery Management System**.

The Digital Twin is therefore designed around functions that are directly relevant to BMS operation:

```text
Battery Modeling & Simulation
            ↓
Battery Monitoring
            ↓
State Estimation
            ↓
Condition Monitoring
            ↓
Fault / Warning Monitoring
            ↓
Validation & Analysis
            ↓
Communication & Visualization
```

The main BMS-oriented variables and information considered by the system are:

* Pack voltage
* Battery current
* Cell or pack temperature
* State of Charge (SoC)
* Estimated internal states
* Power demand
* Relevant battery model parameters
* Battery operating condition
* BMS status information
* Fault information
* Warning information

The exact set of monitored variables will be finalized during the system requirements and architecture phases.

Where it is useful, the architecture can also distinguish between **pack-level measurements** and **cell-level measurements**. This keeps the system open to more detailed BMS-oriented monitoring without requiring a complete redesign of the architecture later.

---

## 4. Digital Twin Definition for This Project

In this project, the Digital Twin is not simply a static simulation model.

I am defining it as a **dynamic software representation of the EV battery system** that connects the virtual battery model with state estimation, monitoring, validation, communication, and visualization.

The intended relationship is:

```text
Virtual Battery System
        ↕
MATLAB / Simulink
        ↕
Battery Model
        ↕
C++ / Eigen EKF
        ↕
BMS State Estimation
        ↕
Condition / Fault Monitoring
        ↕
Python Validation
        ↕
MQTT / IIoT
        ↕
FastAPI Backend
        ↕
React Web HMI
```

The Digital Twin should therefore provide continuously updated information about the battery's operating state.

It can support:

* Battery simulation
* Algorithm development
* BMS state estimation
* Validation
* Data analysis
* Real-time monitoring
* Condition and fault visualization
* Digital representation of battery operating states
* Simulation-based testing
* Future integration with physical battery or BMS data

The Digital Twin is therefore acting as a **model-based software representation and supervisory layer around the battery/BMS domain**.

It is important to define one boundary from the beginning:

> This project does not replace the safety-critical protection functions of a physical BMS.

The project is intended for modeling, estimation, monitoring, diagnostics, validation, visualization, and software integration. It is not a production-ready safety-critical BMS.

---

## 5. Project Scope

### 5.1 Included in the Scope

The project includes the complete software chain required to build the Digital Twin:

### Battery Modeling

I will develop the mathematical and computational battery representation using:

* MATLAB
* Simulink

The model will represent battery behavior under defined operating conditions and drive-cycle scenarios.

### State Estimation

I will investigate and implement an **Extended Kalman Filter (EKF)** for battery state estimation.

The main target is SoC estimation, together with other internal states that can be estimated from the selected battery model.

The estimator will be implemented in:

* C++
* Eigen

### BMS Monitoring

The system will process and monitor important battery variables such as:

* Voltage
* Current
* Temperature
* SoC
* Power
* Internal estimated states
* Operating condition
* Warnings
* Fault indicators

### Validation

Validation and engineering data analysis will be implemented in Python.

This includes:

* Reference data processing
* Simulation result processing
* EKF output analysis
* Estimation error calculation
* Statistical analysis
* Test scenario evaluation
* Performance comparison
* Result visualization
* Automated validation workflows

### Data Exchange

JSON will be used as a structured representation for data moving between different software components where appropriate.

### IIoT Communication

The communication layer will use:

* MQTT
* Python
* Paho MQTT

This layer will separate the computational components from the application and visualization layers.

### Backend

The application/backend layer will use:

* FastAPI

### Web-Based HMI and Dashboard

The monitoring environment will be completely web-based.

The frontend will use:

* React

The web application will provide the software equivalent of an industrial monitoring HMI and dashboard.

### UI/UX Design

Before implementation, the interface will be planned using:

* Figma

Figma will be used for:

* Wireframes
* Screen planning
* Dashboard structure
* HMI design
* Component planning
* User interaction design
* Visual hierarchy

### Project Management and Documentation

The project will use:

* Git
* GitHub
* Markdown

for version control, collaboration, and technical documentation.

---

## 6. Project Boundaries

One of the most important decisions in Day 0 is defining what the project is **and what it is not**.

This project is intentionally **software-only**.

There is no requirement to implement:

* Physical BMS hardware
* Embedded controllers
* PLC hardware
* Industrial HMI panels
* Physical sensors
* Dedicated battery measurement hardware
* Other dedicated physical hardware

The battery system is represented through a mathematical and simulation model.

The HMI is also entirely software-based and runs as a web application.

Therefore, the basic boundary is:

```text
                 PROJECT SCOPE
┌─────────────────────────────────────────────┐
│ Mathematical Battery Model                 │
│ MATLAB / Simulink                           │
│                                             │
│ BMS State Estimation                        │
│ C++ / Eigen / EKF                           │
│                                             │
│ Monitoring & Condition Detection            │
│                                             │
│ Python Validation & Data Analysis           │
│                                             │
│ JSON Data Exchange                           │
│                                             │
│ MQTT / IIoT Communication                   │
│                                             │
│ FastAPI Backend                              │
│                                             │
│ React Web HMI & Dashboard                   │
│                                             │
│ Figma UI/UX Design                           │
└─────────────────────────────────────────────┘

                 OUTSIDE SCOPE

┌─────────────────────────────────────────────┐
│ Physical BMS Hardware                       │
│ Embedded Hardware                            │
│ PLC Hardware                                 │
│ Industrial HMI Hardware                     │
│ Physical Sensors                             │
└─────────────────────────────────────────────┘
```

This boundary keeps the project technically focused while still allowing the architecture to be extended toward real battery/BMS data in the future.

---

## 7. Main Project Objectives

The project objectives are:

### Battery Model

1. Develop a mathematical and computational model of a Li-ion EV battery pack suitable for BMS-oriented applications.
2. Implement the battery simulation environment in MATLAB/Simulink.
3. Define the required battery variables, model states, parameters, and operating scenarios.

### BMS-Oriented Estimation

4. Define a BMS-oriented battery monitoring architecture.
5. Implement real-time SoC estimation using an Extended Kalman Filter.
6. Develop the estimator core in C++ using Eigen.
7. Estimate internal battery states that cannot be directly measured.

### Monitoring and Diagnostics

8. Monitor voltage, current, temperature, SoC, power, and relevant internal states.
9. Establish the foundation for BMS-oriented condition monitoring.
10. Establish the foundation for warning and fault detection.

### Validation

11. Build a Python-based validation pipeline.
12. Compare the estimator results with reference data.
13. Calculate estimation errors and evaluate performance.
14. Analyze model and estimator behavior under defined test scenarios.
15. Generate engineering plots and validation results.

### Data and Communication

16. Generate structured JSON data.
17. Establish MQTT-based IIoT communication.
18. Implement MQTT publishing and subscription using Paho MQTT.
19. Define a clear data pipeline between simulation, estimation, validation, communication, backend, and visualization.

### Web Application

20. Develop a fully web-based monitoring environment.
21. Implement the application/backend layer using FastAPI.
22. Implement the frontend, HMI, and dashboard using React.
23. Design the interface using Figma before implementation.

### System Integration

24. Connect the complete software pipeline.
25. Perform end-to-end integration testing.
26. Validate the complete Digital Twin system.

---

## 8. High-Level System Concept

At a high level, the system is structured as follows:

```text
                    EV Battery Pack Model
                         MATLAB/Simulink
                                │
                                ▼
                 Voltage / Current / Temperature
                                │
                                ▼
                       Data Acquisition
                                │
                                ▼
                         Battery Model
                         MATLAB/Simulink
                                │
                                ▼
                         EKF Estimator
                         C++ / Eigen
                         SoC / States
                                │
                                ▼
                    BMS State & Fault Monitoring
                                │
                                ▼
                Python Validation / Data Analysis
                         / JSON Generation
                                │
                                ▼
                          MQTT / IIoT
                        Python / Paho
                                │
                                ▼
                     Web Application / Backend
                                │
                         ┌──────┴──────┐
                         ▼             ▼
                    Web-Based HMI   Web Dashboard
```

The architecture is modular.

This means that each major component has a specific responsibility and a defined interface with the other components.

The purpose of this separation is to make the system:

* Easier to develop
* Easier to test
* Easier to replace
* Easier to debug
* Easier to validate
* Easier to extend

A change in one subsystem should not require redesigning the entire project.

---

## 9. Technology Responsibility

Each technology has a specific role in the system.

| Technology     | Main Responsibility                                                                                                            |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **MATLAB**     | Mathematical modeling, battery equations, numerical modeling, and model development                                            |
| **Simulink**   | Dynamic battery simulation, drive-cycle execution, and simulation environment                                                  |
| **C++**        | Core real-time estimation implementation                                                                                       |
| **Eigen**      | Matrix, vector, covariance, Jacobian-related, and numerical operations inside the estimator                                    |
| **EKF**        | BMS-oriented SoC and internal state estimation                                                                                 |
| **Python**     | Validation, data analysis, test automation, result processing, data generation, and JSON generation                            |
| **NumPy**      | Numerical computation, array operations, and numerical data processing                                                         |
| **Pandas**     | Dataset organization, filtering, comparison, and time-series analysis                                                          |
| **Matplotlib** | Engineering plots and validation visualizations                                                                                |
| **SciPy**      | Scientific computing, numerical analysis, signal/data processing, interpolation, optimization, and supporting validation tasks |
| **Paho MQTT**  | MQTT publishing and subscription from Python                                                                                   |
| **MQTT**       | Decoupled messaging between software components                                                                                |
| **JSON**       | Structured data exchange between system layers                                                                                 |
| **FastAPI**    | Application and backend API layer                                                                                              |
| **React**      | Web-based HMI and monitoring dashboard                                                                                         |
| **Figma**      | Wireframing, UI planning, interaction design, and interface design                                                             |
| **Git**        | Version control                                                                                                                |
| **GitHub**     | Repository management and project collaboration                                                                                |
| **Markdown**   | Technical documentation                                                                                                        |

The separation is intentional.

I do not want every technology to do everything. Each tool is selected for a specific responsibility so that the project remains modular and traceable.

---

## 10. Battery System Definition

The target system is a:

> **355-V-class Li-ion EV battery pack**

The physical battery is not implemented in this project.

Instead, the battery is represented through a **software-based mathematical and simulation model**.

The Digital Twin will represent battery information relevant to BMS operation, including:

```text
Pack Voltage
     │
Battery Current
     │
Temperature
     │
State of Charge
     │
Estimated Internal States
     │
Power
     │
Battery Model Parameters
     │
Operating Condition
     │
BMS Status
     │
Warnings
     │
Fault Information
```

The final list of monitored variables will be defined in the requirements and architecture phases.

The architecture is also intentionally open to both:

* Pack-level information
* Cell-level information

This gives the project a path toward more detailed BMS-oriented monitoring later.

---

## 11. Battery Modeling and Simulation

The battery model is the foundation of the Digital Twin.

I will use **MATLAB** for the mathematical formulation and numerical implementation of the battery model.

I will use **Simulink** as the dynamic simulation environment.

The simulation layer will generate battery behavior under defined operating conditions and drive-cycle scenarios.

Possible outputs include:

* Battery voltage
* Battery current
* Temperature
* State of Charge
* Internal model states
* Power
* Battery model parameters
* Drive-cycle response

These outputs will then be used by the downstream state estimation and validation layers.

The battery simulation therefore acts as the **virtual plant** of the Digital Twin.

---

## 12. State Estimation Concept

A major part of the project is the estimation of battery states that cannot be directly measured.

For this purpose, I will investigate and implement an **Extended Kalman Filter (EKF)**.

The estimator combines:

* Battery measurements
* The mathematical battery model
* Recursive state prediction
* Measurement updates

The general estimation process is:

```text
Measurements
      ↓
Prediction
      ↓
State Estimate
      ↓
Measurement Update
      ↓
Updated State
```

The EKF will primarily be used for **SoC estimation**, while other internal battery states may also be estimated depending on the selected battery model.

The estimator core will be implemented in **C++**.

**Eigen** will be used for the required linear algebra operations, including:

* Matrix operations
* Vector operations
* Covariance calculations
* Jacobian-related computations
* Other numerical operations required by the EKF

The estimator output will then be available to the monitoring, validation, communication, and visualization layers.

---

## 13. Validation and Data Analysis

I am separating validation from the estimator implementation on purpose.

The EKF will run in C++, while its performance will be independently evaluated using Python.

Python will handle:

* Reference data processing
* Simulation result processing
* EKF output analysis
* Estimation error calculation
* Statistical analysis
* Test scenario evaluation
* Performance comparison
* Result visualization
* Automated validation
* JSON generation

The main Python libraries are:

### NumPy

Used for numerical computation, array operations, matrix manipulation, and numerical processing.

### Pandas

Used for structured datasets, filtering, comparison, time-series analysis, and data organization.

### Matplotlib

Used to generate engineering and validation plots such as:

* SoC comparison
* Voltage and current plots
* Estimation error plots
* Temperature plots
* Model-versus-reference plots
* Validation results

### SciPy

Used where additional scientific and numerical computation is required, including:

* Numerical analysis
* Signal processing
* Interpolation
* Optimization
* Supporting validation algorithms

The main principle is:

> The estimator should not be trusted simply because it runs. Its behavior must be independently evaluated against reference data and defined test scenarios.

---

## 14. JSON Data Layer

JSON is the main structured data representation between different software components where appropriate.

Python will be responsible for generating, processing, and validating JSON data.

JSON may contain:

* Battery state data
* Estimated state data
* Test scenarios
* Configuration data
* Validation results
* MQTT payloads
* Backend/API communication data

The purpose of this layer is to create a consistent data representation between:

```text
Simulation
    ↕
Estimation
    ↕
Validation
    ↕
Communication
    ↕
Backend
    ↕
Web Application
```

This also makes the project easier to integrate and test because the data format is structured and explicit.

---

## 15. Real-Time Data Flow

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
Python Validation    JSON Data
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
          ┌────┴────┐
          ▼         ▼
     Web-Based HMI  Web Dashboard
```

The data flow is intentionally separated into the following stages:

1. Battery simulation
2. Data acquisition
3. State estimation
4. BMS-oriented state and condition monitoring
5. Validation and analysis
6. Structured data generation
7. MQTT communication
8. Backend processing
9. Web-based visualization

This separation means that the estimation and visualization layers do not need to be tightly coupled to the underlying battery simulation.

---

## 16. IIoT and MQTT Communication

The IIoT communication layer will be based on **MQTT**.

Python and **Paho MQTT** will be used for publishing and subscribing to messages.

The purpose of MQTT is to decouple the computational side of the project from the application and visualization side.

A possible topic hierarchy is:

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

The final topic hierarchy and message schema will be defined later during the communication architecture phase.

JSON will be used as the structured payload format where appropriate.

---

## 17. Web-Based HMI and Dashboard

The project does not use a dedicated industrial HMI panel.

Instead, the monitoring environment is implemented completely as a **web-based HMI**.

The web application is intended to provide the software equivalent of an industrial monitoring interface.

The web layer will provide information such as:

* Real-time battery monitoring
* SoC visualization
* Voltage monitoring
* Current monitoring
* Temperature monitoring
* Power monitoring
* BMS state display
* Warning visualization
* Fault visualization
* Historical data visualization
* System status
* Digital Twin status and information

The planned software stack is:

```text
React
  ↓
Web HMI / Dashboard
  ↓
FastAPI
  ↓
MQTT
  ↓
Computational / Communication Layers
```

Therefore, the web application is both:

* The **HMI**
* The main **visualization environment**

of the project.

---

## 18. UI/UX Design Process

The interface will be designed in **Figma before implementation**.

The design process is:

```text
Requirements
     ↓
Figma Wireframe
     ↓
UI / UX Design
     ↓
React Implementation
     ↓
FastAPI Integration
     ↓
Real-Time MQTT Data
     ↓
Web-Based BMS HMI
```

Figma will be used for:

* Wireframing
* UI layout planning
* Dashboard structure
* HMI screen design
* Component planning
* User interaction design
* Visual hierarchy

The final designs will then be translated into the React implementation.

This separates interface planning from coding and makes the web layer easier to structure before implementation.

---

## 19. Overall Software Architecture

At Day 0, I define the complete system as the following logical chain:

```text
┌──────────────────────────────────────────────┐
│            Battery Virtual Plant             │
│               MATLAB/Simulink               │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│             BMS State Estimation             │
│               C++ / Eigen / EKF              │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│       BMS State & Condition Monitoring       │
│          Warnings / Fault Indicators         │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│         Python Validation & Analysis         │
│       NumPy / Pandas / Matplotlib / SciPy    │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│             Structured Data Layer            │
│                     JSON                     │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│                IIoT Layer                    │
│            MQTT / Paho MQTT                  │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│              Application Layer               │
│                   FastAPI                    │
└──────────────────────┬───────────────────────┘
                       ↓
┌──────────────────────────────────────────────┐
│             Web Application Layer            │
│                    React                    │
│          Web HMI + Dashboard                 │
└──────────────────────────────────────────────┘
```

This architecture is the basis for the next development phases.

---

## 20. Project Structure

The planned repository structure is:

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

### Directory Responsibilities

`docs/`

Contains project documentation, BMS architecture, engineering decisions, requirements, and validation documentation.

`src/`

Contains the main source code, including C++ estimation, monitoring, diagnostics, and communication components.

`models/`

Contains MATLAB/Simulink mathematical and battery models.

`tests/`

Contains unit, integration, estimator, simulation, and validation tests.

`dashboard/`

Contains the React-based web monitoring interface and web HMI.

`data/`

Contains simulation, reference, validation, and processed datasets.

`config/`

Contains system, battery, estimator, communication, and application configuration.

`scripts/`

Contains Python validation, data-processing, JSON-generation, and automation scripts.

`results/`

Contains simulation, estimation, experimental, and validation results.

---

## 21. Development Methodology

I will not build the entire system at once.

The project will be developed incrementally through defined engineering phases.

### Phase 0 — Project Definition

The purpose of this phase is to establish the foundation of the project.

It includes:

* Problem statement
* BMS-oriented problem definition
* Project objectives
* Scope
* Constraints
* Software-only system definition

This document represents **Day 0**.

---

### Phase 1 — System Requirements

After defining the project, the next step is to define what the system must do.

This phase includes:

* Functional requirements
* Non-functional requirements
* BMS monitoring requirements
* State estimation requirements
* Performance requirements
* Communication requirements
* Web HMI requirements
* Security requirements

---

### Phase 2 — System Architecture

The system architecture will then define how the complete software system is organized.

This phase includes:

* Overall architecture
* BMS-oriented system architecture
* Data flow
* Component interfaces
* Communication architecture
* Monitoring interfaces
* Diagnostic interfaces
* Software integration architecture

---

### Phase 3 — Battery Modeling

The battery model will then be developed and validated.

This phase includes:

* Battery model selection
* Mathematical formulation
* Parameter definition
* MATLAB implementation
* Simulink implementation
* Drive-cycle simulation
* Model validation
* BMS-oriented model requirements

---

### Phase 4 — State Estimation

The estimator will then be developed.

This phase includes:

* EKF formulation
* State-space representation
* BMS state estimation requirements
* C++ implementation
* Eigen integration
* Estimator validation

---

### Phase 5 — IIoT Communication

The communication layer will then be implemented.

This includes:

* MQTT architecture
* BMS data topic structure
* JSON message format
* Paho MQTT implementation
* Data publishing
* Data subscription
* Battery state transmission
* Condition and fault data transmission

---

### Phase 6 — HMI and Web Monitoring

The monitoring application will then be developed.

This includes:

* Figma wireframes
* UI/UX design
* Web-based HMI
* React frontend
* FastAPI backend
* BMS monitoring interface
* Real-time visualization
* Battery state monitoring
* Alarm monitoring
* Status monitoring

---

### Phase 7 — System Integration

After individual components are developed, they will be integrated.

This includes:

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

---

### Phase 8 — Validation and Documentation

The final phase focuses on proving that the system works as intended.

This includes:

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

## 22. Engineering Principles

I want the project to follow clear engineering principles rather than becoming a collection of code that simply works.

### 1. Modularity

Every major subsystem should have a clear responsibility and interface.

### 2. BMS Orientation

Battery monitoring, state estimation, condition monitoring, and diagnostics should be designed around realistic BMS engineering requirements.

### 3. Software-Only Architecture

The project is a software Digital Twin and does not require dedicated physical BMS, PLC, HMI, sensor, or embedded hardware.

### 4. Technology Separation

MATLAB/Simulink, C++, Python, MQTT, backend, and frontend technologies should have clearly defined responsibilities.

### 5. Traceability

Engineering decisions should be documented and connected to system requirements.

### 6. Testability

Individual components should be independently testable whenever possible.

### 7. Reproducibility

Simulation, validation, and analysis results should be reproducible using documented configurations and datasets.

### 8. Security by Design

Communication and monitoring components should consider authentication, authorization, and secure data handling.

### 9. Separation of Concerns

Battery modeling, state estimation, validation, communication, backend processing, and visualization should remain modular and independently maintainable.

### 10. Incremental Development

The system should be developed and validated in controlled stages instead of being built as one large monolithic application.

### 11. Model-Based Engineering

Battery models and estimation algorithms should form the computational foundation of the Digital Twin and its BMS-oriented functions.

### 12. Independent Validation

The C++ estimation layer should be independently validated using Python-based numerical analysis and reference data.

---

## 23. Current Project Status

At the beginning of the project, the current status is:

**Project Phase: Day 0 — Project Definition**

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
* Full system integration
* Final validation

---

## 24. What Success Means for This Project

The project should not be considered successful just because every technology in the stack has been used.

The actual goal is to create a connected engineering system in which the different technologies have clear and meaningful responsibilities.

The intended final relationship is:

```text
MATLAB / Simulink
        ↓
Accurate Battery Representation
        ↓
C++ / Eigen EKF
        ↓
BMS-Oriented State Estimation
        ↓
Condition / Fault Monitoring
        ↓
Python Validation
        ↓
Validated Data
        ↓
JSON
        ↓
MQTT / IIoT
        ↓
FastAPI
        ↓
React
        ↓
Web-Based BMS HMI
```

The final Digital Twin should therefore be able to represent the dynamic battery behavior, estimate important internal states, evaluate the results, communicate the information between software layers, and present the important battery/BMS information through a web interface.

The system should also be structured so that individual components can be tested independently and the complete pipeline can be validated end-to-end.

---

## 25. Final Day 0 Definition

At the end of Day 0, I define the project as follows:

> I am building a **software-based, BMS-oriented real-time Digital Twin for a 355-V-class Li-ion EV battery pack**.
>
> MATLAB/Simulink will provide the virtual battery plant and mathematical simulation environment. C++ and Eigen will implement the real-time EKF-based state estimation layer. Python will be responsible for validation, numerical analysis, data processing, visualization, automation, and JSON generation. MQTT and Paho MQTT will provide the IIoT communication layer. FastAPI will provide the backend/API layer, while React will provide the web-based HMI and monitoring dashboard. Figma will be used for interface planning and UI/UX design.
>
> The project is intentionally hardware-independent. No physical BMS, PLC, industrial HMI, sensors, embedded controller, or dedicated hardware is part of the implementation.
>
> The main engineering purpose is to connect **battery modeling, BMS-oriented state estimation, condition monitoring, validation, communication, and visualization** into one modular Digital Twin architecture.
>
> The Digital Twin will act as a **dynamic software representation and supervisory layer around the battery/BMS domain**, while remaining clearly separate from the safety-critical protection functions of a physical BMS.
>
> Development will follow an incremental engineering process beginning with requirements and architecture, followed by battery modeling, EKF implementation, communication, web HMI development, integration, validation, and final documentation.

---

## 26. Day 0 Deliverable

The main deliverable of Day 0 is a clear and stable project definition that answers these questions:

```text
What am I building?
        ↓
A BMS-oriented real-time Digital Twin
for a 355-V-class Li-ion EV battery pack

Why am I building it?
        ↓
To combine battery modeling, state estimation,
monitoring, diagnostics, validation,
communication, and visualization

How will I build it?
        ↓
MATLAB/Simulink
C++ / Eigen / EKF
Python
JSON
MQTT / Paho MQTT
FastAPI
React
Figma

What is the system boundary?
        ↓
Software-only
No dedicated physical hardware

What is the development strategy?
        ↓
Requirements
→ Architecture
→ Modeling
→ Estimation
→ Communication
→ HMI
→ Integration
→ Validation
```

This establishes the baseline for **Day 1 and all following development phases**.
