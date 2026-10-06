# Real-Time Digital Twin of a 355-V-Class Li-ion EV Battery Pack

## Overview

This project focuses on the design and implementation of a real-time Digital Twin for a 355-V-class Lithium-ion Electric Vehicle (EV) battery pack.

The system is designed to combine battery modeling, real-time state estimation, Industrial Internet of Things (IIoT) communication, secure Human-Machine Interface (HMI), and web-based monitoring within a unified architecture.

The primary objective is to develop a modular and extensible digital representation of an EV battery pack that can continuously receive operational data, estimate internal battery states, and expose meaningful information through an industrial-style monitoring interface.

---

## Project Objectives

The project aims to:

- Develop a mathematical and computational model of an EV Li-ion battery pack.
- Implement real-time State of Charge (SoC) estimation using an Extended Kalman Filter (EKF).
- Develop the core estimation components in C++.
- Establish IIoT communication using the MQTT protocol.
- Design a secure industrial HMI for battery monitoring.
- Develop a web-based monitoring dashboard.
- Establish a structured data pipeline between the physical battery representation, estimation layer, communication layer, and visualization layer.
- Validate the performance of the Digital Twin against reference data and defined test scenarios.

---

## System Concept

The conceptual architecture of the system is:

```text
             EV Battery Pack
                    │
          Voltage / Current /
          Temperature / Other Data
                    │
                    ▼
          ┌──────────────────┐
          │ Data Acquisition │
          └────────┬─────────┘
                   │
                   ▼
          ┌──────────────────┐
          │  Battery Model   │
          └────────┬─────────┘
                   │
                   ▼
          ┌──────────────────┐
          │  EKF Estimator   │
          │  SoC / States    │
          └────────┬─────────┘
                   │
                   ▼
          ┌──────────────────┐
          │ MQTT / IIoT Layer│
          └────────┬─────────┘
                   │
          ┌────────┴─────────┐
          ▼                  ▼
   Industrial HMI       Web Dashboard
```

The architecture is modular so that individual components can be tested, replaced, or extended without redesigning the entire system.

---

## Core Technologies

| Area | Technology / Method |
|---|---|
| Programming | C++ |
| State Estimation | Extended Kalman Filter (EKF) |
| Battery Modeling | Equivalent Circuit / Mathematical Battery Model |
| Communication | MQTT |
| Industrial Connectivity | IIoT |
| Visualization | Web-Based Dashboard |
| HMI | Industrial HMI |
| Version Control | Git |
| Repository | GitHub |
| Documentation | Markdown |

Additional technologies may be introduced as the implementation evolves.

---

## Battery System

The target system represents a 355-V-class Li-ion EV battery pack.

The Digital Twin is intended to work with operational variables such as:

- Pack voltage
- Battery current
- Cell or pack temperature
- State of Charge (SoC)
- Estimated internal states
- Power demand
- Relevant model parameters

The final set of monitored variables will be defined during the system requirements and architecture phases.

---

## State Estimation

A central component of the project is real-time battery state estimation.

An Extended Kalman Filter (EKF) will be investigated and implemented for estimating battery states that cannot be directly measured.

The estimator will use available measurements and the battery model to recursively estimate the internal state of the battery.

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

The implementation and validation of the EKF will be documented separately within the project documentation.

---

## Real-Time Data Flow

The intended data pipeline is:

```text
Battery / Simulation Data
          │
          ▼
     Data Acquisition
          │
          ▼
      Battery Model
          │
          ▼
     EKF Estimation
          │
          ▼
        MQTT
          │
     ┌────┴────┐
     ▼         ▼
    HMI      Web UI
```

This architecture allows the estimation and visualization layers to remain decoupled from the underlying data source.

---

## Digital Twin Concept

The Digital Twin is not limited to a static battery model.

The intended system continuously connects:

```text
Physical / Simulated System
          ↕
     Data Acquisition
          ↕
    Mathematical Model
          ↕
     State Estimation
          ↕
   Communication Layer
          ↕
 Monitoring & Visualization
```

This enables the Digital Twin to represent the dynamic behavior of the battery system and provide continuously updated estimated states.

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

- `docs/` — Project documentation and engineering decisions.
- `src/` — Main source code.
- `models/` — Battery and mathematical models.
- `tests/` — Unit, integration, and validation tests.
- `dashboard/` — Web-based monitoring interface.
- `data/` — Experimental, simulated, or reference datasets.
- `config/` — System and application configuration.
- `scripts/` — Utility and automation scripts.
- `results/` — Experimental and validation results.

---

## Development Methodology

The project will be developed incrementally through defined engineering phases.

### Phase 0 — Project Definition

- Problem Statement
- Objectives
- Scope
- Constraints

### Phase 1 — System Requirements

- Functional requirements
- Non-functional requirements
- Performance requirements
- Communication requirements
- Security requirements

### Phase 2 — System Architecture

- Overall architecture
- Data flow
- Component interfaces
- Communication architecture

### Phase 3 — Battery Modeling

- Battery model selection
- Parameter definition
- Model implementation
- Model validation

### Phase 4 — State Estimation

- EKF formulation
- State-space representation
- C++ implementation
- Estimator validation

### Phase 5 — IIoT Communication

- MQTT architecture
- Topic structure
- Message format
- Data publishing and subscription

### Phase 6 — HMI & Web Monitoring

- Industrial HMI
- Web dashboard
- Real-time visualization
- Alarm and status monitoring

### Phase 7 — Integration

- End-to-end data flow
- Component integration
- Real-time testing
- Error handling

### Phase 8 — Validation & Documentation

- Test scenarios
- Estimation accuracy
- System performance
- Results analysis
- Final technical documentation

---

## Current Status

**Project Phase:** Day 0 — Project Definition

### Completed

- Initial project definition
- Repository initialization
- Git version control
- GitHub repository
- Initial project structure
- Project documentation structure

### In Progress

- System requirements
- Detailed architecture
- Battery model definition
- EKF design

### Planned

- C++ implementation
- MQTT communication
- Industrial HMI
- Web-based monitoring
- System integration
- Validation

---

## Engineering Principles

The project follows several principles:

1. **Modularity** — Each major subsystem should have a clear responsibility and interface.
2. **Traceability** — Engineering decisions should be documented and connected to requirements.
3. **Testability** — Components should be independently testable whenever possible.
4. **Reproducibility** — Experiments and results should be reproducible from documented configurations and datasets.
5. **Security by Design** — Communication and monitoring components should consider authentication, authorization, and secure data handling.
6. **Incremental Development** — The system should be built and validated in controlled stages rather than as a single monolithic implementation.

---

## Project Status

This repository represents an active engineering project.

Features described as planned or in progress have not necessarily been implemented or validated yet.

---

## License

License information will be added after the project's distribution and usage requirements are defined.