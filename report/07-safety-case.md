# 7 Safety Case

## 7.1 Introduction

A Safety Case is a structured argument supported by objective evidence demonstrating that a system is acceptably safe for its intended application [4].

Within railway systems, Safety Cases are required to justify that:

* Hazards have been identified.
* Risks have been assessed.
* Safety requirements have been derived.
* Safety mechanisms have been implemented.
* Verification activities have been completed.
* Residual risks remain acceptable.

According to EN 50129, a Safety Case must provide sufficient evidence that safety objectives are achieved throughout the system lifecycle [4].

For this project, the Safety Case focuses on demonstrating that the RaSTA communication architecture can safely support communication between railway applications operating over non-safe communication networks.

The Safety Case developed in this section uses:

* Hazard Analysis (Section 4)
* System Architecture (Section 5)
* Verification Evidence (Section 6)
* Fault Tree Analysis
* Residual Risk Assessment

to justify the safety of the proposed communication system.

---

## 7.2 Top-Level Safety Claim

### Safety Claim SC-1

**The RaSTA communication system prevents hazardous communication failures from causing unsafe railway system behaviour.**

This claim represents the primary safety objective of the communication architecture.

To justify this claim, four subordinate claims are established.

---

### SC-1.1 Hazard Identification and Control

**Claim**

Communication hazards have been identified and appropriate mitigation measures have been implemented.

**Supporting Evidence**

* Hazard Identification (Section 4.2)
* Risk Assessment (Section 4.4)
* Safety Function Derivation (Section 4.5)

References:

[1][3][10]

---

### SC-1.2 Communication Failures are Detected

**Claim**

Communication failures are detected before information is delivered to the application layer.

**Supporting Evidence**

* CRC Verification
* Sequence Supervision
* Time Supervision
* Endpoint Validation

References:

[5][6]

---

### SC-1.3 Fault Handling is Deterministic

**Claim**

Detected communication failures result in predictable and safe protocol behaviour.

**Supporting Evidence**

* Connection Supervision
* Safe-State Activation
* Session Management

References:

[1][4][6]

---

### SC-1.4 Residual Risk is Acceptable

**Claim**

The probability of hazardous communication failures remains within acceptable limits.

**Supporting Evidence**

* Reliability Analysis
* Availability Analysis
* Fault Tree Analysis
* THR Assessment

References:

[4][8][9]

---

## 7.3 Safety Argument Structure

The Safety Case can be represented using a goal-based argument structure.

```text
SC-1
│
├── SC-1.1 Hazards Identified
│
├── SC-1.2 Communication Errors Detected
│
├── SC-1.3 Safe Failure Behaviour
│
└── SC-1.4 Residual Risk Acceptable
```

This structure follows the argumentation principles commonly applied in railway safety assessments [4].

---

## 7.4 Safety Evidence Summary

The Safety Case relies upon evidence collected throughout the project lifecycle.

### Hazard Analysis Evidence

Provided by:

* Section 4

Demonstrates:

* Hazard identification
* Risk classification
* Safety function derivation

---

### Architectural Evidence

Provided by:

* Section 5

Demonstrates:

* Safety mechanism implementation
* Architectural traceability
* Hazard mitigation structure

---

### Verification Evidence

Provided by:

* Section 6

Demonstrates:

* Correct protocol operation
* Reliability achievement
* Availability achievement
* Requirement compliance

---

### Quantitative Evidence

Provided by:

* Reliability calculations
* CRC analysis
* Reliability Block Diagram analysis

Demonstrates:

* Dependability performance
* Error detection effectiveness

---

## 7.5 Safety Requirement Satisfaction

The safety requirements established in Section 3 can be evaluated against available evidence.

| Safety Requirement                        | Evidence                  |
| ----------------------------------------- | ------------------------- |
| SR-1 Corrupted Message Rejection          | CRC Analysis              |
| SR-2 Duplicate Message Rejection          | Sequence Monitoring       |
| SR-3 Sequence Error Detection             | Sequence Verification     |
| SR-4 Delay Detection                      | Time Supervision          |
| SR-5 Communication Interruption Detection | Connection Monitoring     |
| SR-6 Invalid Endpoint Detection           | Endpoint Validation       |
| SR-7 Safe-State Activation                | Fault Handling Procedures |

The analysis demonstrates that every safety requirement is supported by at least one dedicated safety mechanism.

---

## 7.6 Functional Safety Assessment Perspective

Functional Safety requires that:

1. Hazards are identified.
2. Risks are evaluated.
3. Safety requirements are defined.
4. Safety functions are implemented.
5. Verification activities are completed.
6. Residual risks are assessed.

The project has completed all six activities.

This demonstrates compliance with the Functional Safety lifecycle described in IEC 61508 [1].

References:

[1][3][4]
