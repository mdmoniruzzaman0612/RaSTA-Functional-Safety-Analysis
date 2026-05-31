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

## 7.7 Fault Tree Analysis (FTA)

### 7.7.1 Introduction

Fault Tree Analysis (FTA) is a top-down deductive analysis technique used to identify combinations of failures that can result in a hazardous event [9].

FTA is widely used in Functional Safety and railway engineering because it provides:

* Structured hazard analysis
* Quantitative risk estimation
* Identification of critical failure paths
* Support for Safety Case development

The purpose of the analysis is to evaluate the probability of hazardous communication failures occurring despite the protection mechanisms implemented within the RaSTA protocol.

---

### 7.7.2 Definition of the Top Event

The top event considered in this project is:

**T0 – Hazardous railway command accepted despite communication failure**

Examples include:

* Corrupted command accepted
* Incorrect command accepted
* Delayed command accepted
* Unauthorized command accepted

If such an event were to occur, incorrect information could potentially influence the behaviour of a safety-related railway application.

References:

[5][6]

---

### Figure 7.2 – Fault Tree Analysis

![Figure 7.2 – Fault Tree Analysis](../figures/figure-7-2-fta.png)

**Figure 7.2:** Fault Tree Analysis of hazardous communication failure within the RaSTA architecture [9].

---

### 7.7.3 Intermediate Events

The top event can occur through three primary failure paths.

#### Event E1 – Corrupted Message Accepted

A corrupted message reaches the application layer despite CRC protection.

This requires:

* Communication corruption occurs
* CRC detection fails

Both conditions must occur simultaneously.

---

#### Event E2 – Sequence Error Accepted

A sequence-related communication fault reaches the application layer.

This requires:

* Sequence error occurs
* Sequence verification fails

Both conditions must occur simultaneously.

---

#### Event E3 – Delayed Message Accepted

An outdated message reaches the application layer.

This requires:

* Excessive transmission delay occurs
* Timeout supervision fails

Both conditions must occur simultaneously.

---

### 7.7.4 Fault Tree Interpretation

The fault tree demonstrates that hazardous communication behaviour requires:

1. A communication fault.
2. Failure of the corresponding protection mechanism.

This is a direct consequence of the defense-in-depth design philosophy applied within the RaSTA architecture.

The probability of a hazardous communication event is therefore significantly lower than the probability of individual communication faults.

References:

[7][9]

---

## 7.8 Quantitative Fault Tree Analysis

### 7.8.1 Assumed Event Probabilities

To estimate the probability of the top event, conservative assumptions are adopted.

| Event                         | Probability  |
| ----------------------------- | ------------ |
| Communication Corruption      | 10⁻⁵         |
| CRC Failure                   | 2.33 × 10⁻¹⁰ |
| Sequence Verification Failure | 10⁻⁶         |
| Timeout Supervision Failure   | 10⁻⁶         |

These assumptions intentionally represent conservative conditions.

---

### 7.8.2 Probability of Event E1

For Event E1:

```text
P(E1)
=
P(Corruption)
×
P(CRC Failure)
```

Substituting:

```text
P(E1)
=
10^-5 × 2.33 × 10^-10
```

Result:

```text
P(E1)
=
2.33 × 10^-15
```

---

### 7.8.3 Probability of Event E2

For Event E2:

```text
P(E2)
=
10^-5 × 10^-6
```

Result:

```text
P(E2)
=
10^-11
```

---

### 7.8.4 Probability of Event E3

For Event E3:

```text
P(E3)
=
10^-5 × 10^-6
```

Result:

```text
P(E3)
=
10^-11
```

---

### 7.8.5 Top Event Probability

For rare independent events:

```text
P(T0)
≈
P(E1)
+
P(E2)
+
P(E3)
```

Substituting:

```text
P(T0)
≈
2.33×10^-15
+
10^-11
+
10^-11
```

Result:

```text
P(T0)
≈
2 × 10^-11
```

---

### 7.8.6 Interpretation

The probability of a hazardous communication event is approximately:

```text
2 × 10^-11
```

per communication transaction.

This result demonstrates the effectiveness of the multiple protection layers implemented by the RaSTA protocol.

References:

[9]

---

## 7.9 Tolerable Hazard Rate (THR) Assessment

### 7.9.1 Definition

The Tolerable Hazard Rate (THR) represents the maximum acceptable frequency of hazardous failures within a safety-related system [4].

THR is commonly used within railway Functional Safety assessments to determine whether a system achieves an acceptable level of risk.

---

### 7.9.2 Typical Railway Targets

For safety-related railway functions, tolerable hazard rates typically fall within the range:

```text
10^-9
to
10^-8
failures/hour
```

References:

[4]

---

### 7.9.3 Comparison with Calculated Result

Calculated Hazard Probability:

```text
2 × 10^-11
```

Required Threshold:

```text
10^-9
```

Comparison:

```text
2 × 10^-11
<
10^-9
```

---

### 7.9.4 THR Verification Result

Verification Result:

✔ THR Requirement Satisfied

The estimated hazardous event probability remains significantly below commonly accepted railway safety targets.

This provides strong evidence supporting the safety of the communication architecture.

References:

[4][9]

---

## 7.10 Residual Risk Assessment

### 7.10.1 Introduction

Residual risk represents the risk that remains after all safety measures have been implemented [1].

No engineering system can completely eliminate risk.

Instead, Functional Safety seeks to reduce risk to a level that is As Low As Reasonably Practicable (ALARP).

References:

[1]

---

### 7.10.2 Remaining Risk Sources

Potential residual contributors include:

* Common-cause failures
* Software design defects
* Incorrect configuration
* Maintenance errors
* Human factors
* Unexpected operating conditions

These factors are difficult to eliminate completely and therefore remain contributors to residual risk.

---

### 7.10.3 Risk Reduction Measures

Residual risks are controlled through:

* Verification activities
* Validation activities
* Configuration management
* Change management
* Independent assessment
* Operational procedures

These measures reduce the probability that residual failures result in hazardous consequences.

---

### 7.10.4 Residual Risk Conclusion

The quantitative analyses performed throughout this project indicate that residual communication risk remains extremely low.

The combination of:

* CRC protection
* Sequence supervision
* Time supervision
* Connection supervision
* Safe-state activation

provides a robust safety architecture capable of reducing communication-related hazards to acceptable levels.

Verification Result:

✔ Residual Risk Acceptable

## 7.11 Functional Safety Assessment (FSA)

### 7.11.1 Purpose

EN 50126 and EN 50129 require independent assessment activities throughout the safety lifecycle to ensure that safety objectives are achieved [3][4].

A Functional Safety Assessment (FSA) evaluates whether:

* Safety requirements are complete.
* Hazards are adequately addressed.
* Verification activities are sufficient.
* Safety evidence is acceptable.
* Residual risk remains tolerable.

The purpose of the FSA is to provide confidence that the developed system satisfies its intended safety objectives.

---

### 7.11.2 Assessment Criteria

The following criteria are considered during the assessment.

| Assessment Area | Evaluation Objective                       |
| --------------- | ------------------------------------------ |
| Requirements    | Completeness and traceability              |
| Hazard Analysis | Identification of communication hazards    |
| Risk Assessment | Adequacy of risk reduction measures        |
| Architecture    | Correct implementation of safety functions |
| Verification    | Evidence of requirement satisfaction       |
| Safety Case     | Adequacy of supporting evidence            |

---

### 7.11.3 Assessment Results

| Assessment Item         | Result      |
| ----------------------- | ----------- |
| Requirements Analysis   | Complete    |
| Hazard Analysis         | Complete    |
| Risk Assessment         | Complete    |
| Safety Functions        | Implemented |
| Architecture Evaluation | Complete    |
| Verification Activities | Complete    |
| Safety Case             | Complete    |

Overall Assessment Result:

✔ Functional Safety objectives achieved

References:

[3][4]

---

## 7.12 Configuration Management

### 7.12.1 Purpose

Configuration Management ensures that all safety-related system components remain controlled throughout the system lifecycle [3].

For communication systems, uncontrolled changes may introduce new hazards or invalidate previously completed verification activities.

Consequently, all modifications must be documented and controlled.

---

### 7.12.2 Configuration Management Activities

The configuration management process includes:

* Version identification
* Baseline management
* Change tracking
* Document control
* Configuration auditing

These activities help ensure consistency between requirements, architecture, implementation, and verification evidence.

---

### 7.12.3 Project Configuration Baseline

For this project, the configuration baseline includes:

| Configuration Item         | Description              |
| -------------------------- | ------------------------ |
| Requirements Specification | Section 3                |
| Hazard Analysis            | Section 4                |
| Architecture Design        | Section 5                |
| Verification Evidence      | Section 6                |
| Safety Case                | Section 7                |
| PlantUML Diagrams          | Repository Documentation |

Maintaining a controlled baseline supports future modifications and safety assessments.

References:

[3]

---

## 7.13 Change Management

### 7.13.1 Purpose

Safety-related systems frequently evolve during their operational lifetime.

Any modification has the potential to affect safety performance.

Consequently, EN 50126 and EN 50129 require a structured change management process [3][4].

---

### 7.13.2 Change Management Process

The recommended process consists of:

1. Change Request
2. Impact Analysis
3. Hazard Review
4. Risk Assessment
5. Approval
6. Implementation
7. Verification
8. Baseline Update

---

### 7.13.3 Example Application

Consider the introduction of a new communication timeout parameter.

The following activities would be required:

* Evaluate impact on timing supervision.
* Assess potential hazards.
* Update requirements if necessary.
* Recalculate safety performance.
* Repeat relevant verification activities.

This process ensures that modifications do not unintentionally degrade system safety.

References:

[3][4]

---

## 7.14 End-to-End Traceability Matrix

### 7.14.1 Purpose

One of the most important principles of Functional Safety is traceability [1].

Traceability demonstrates how hazards are connected to:

* Safety requirements
* Safety functions
* Architectural elements
* Verification evidence

This ensures that every identified risk is addressed by an implemented safety mechanism.

---

### 7.14.2 Traceability Matrix

| Hazard                        | Safety Requirement | Safety Function | Architecture Element      | Verification Evidence   |
| ----------------------------- | ------------------ | --------------- | ------------------------- | ----------------------- |
| H1 Message Corruption         | SR-1               | SF-1            | CRC Module                | CRC Analysis            |
| H2 Message Loss               | SR-2               | SF-2            | Sequence Manager          | Fault Injection Testing |
| H3 Message Duplication        | SR-2               | SF-2            | Sequence Manager          | Fault Injection Testing |
| H4 Message Insertion          | SR-6               | SF-5            | Connection Manager        | Session Verification    |
| H5 Incorrect Sequence         | SR-3               | SF-2            | Sequence Manager          | Protocol Testing        |
| H6 Excessive Delay            | SR-4               | SF-3            | Timeout Monitor           | Timing Analysis         |
| H7 Communication Interruption | SR-5               | SF-4            | Connection Manager        | Connection Monitoring   |
| H8 Masquerading               | SR-6               | SF-5            | Endpoint Validation Logic | Session Verification    |

---

### 7.14.3 Traceability Assessment

The matrix demonstrates that:

* Every hazard has been addressed.
* Every safety requirement has an implementation.
* Every implementation has verification evidence.

This provides complete traceability throughout the project lifecycle.

References:

[1][3][4]

---

## 7.15 Final Safety Conclusion

The Safety Case developed throughout this section demonstrates that the proposed RaSTA communication architecture satisfies its intended safety objectives.

The analysis showed that:

* Communication hazards were systematically identified.
* Risks were evaluated and reduced through dedicated safety functions.
* Multiple independent protection mechanisms were implemented.
* Reliability and availability objectives were achieved.
* Fault Tree Analysis demonstrated a very low probability of hazardous communication failure.
* The calculated hazard probability satisfies typical railway THR expectations.
* Residual risk remains acceptable.

The Safety Case therefore provides structured evidence that the communication architecture can support safety-related railway applications operating over non-safe communication networks.

Consequently, the proposed RaSTA architecture can be considered a suitable communication solution for safety-related railway systems and is consistent with the principles of IEC 61508, EN 50126, EN 50129, and EN 50159 [1][3][4][5][6].

---

### Section 7 Summary

| Topic                        | Status   |
| ---------------------------- | -------- |
| Safety Claims                | Complete |
| Safety Argument              | Complete |
| Fault Tree Analysis          | Complete |
| THR Assessment               | Complete |
| Residual Risk Assessment     | Complete |
| Functional Safety Assessment | Complete |
| Configuration Management     | Complete |
| Change Management            | Complete |
| Traceability Matrix          | Complete |
| Safety Conclusion            | Complete |

Section 7 is therefore complete and provides the formal safety justification for the RaSTA communication architecture.
