# 6 Proof of Correct Functional Operation

## 6.1 Introduction

The purpose of this section is to provide objective evidence that the proposed RaSTA communication architecture satisfies the functional, reliability, availability, maintainability, and safety requirements defined in Section 3.

Within Functional Safety, it is not sufficient to define requirements and develop an architecture. According to IEC 61508 and EN 50126, it must also be demonstrated that the implemented system performs its intended functions correctly and that the probability of hazardous failures remains within acceptable limits throughout the system lifecycle [1][3].

For communication systems used in railway applications, proof of correct operation is particularly important because communication failures may directly affect signalling, train control, interlocking systems, and other safety-related functions [4]. Consequently, communication mechanisms must be analysed not only from a functional perspective but also from a reliability and safety perspective.

The objectives of this section are therefore to:

* Verify that communication requirements are satisfied.
* Demonstrate correct operation of safety mechanisms.
* Evaluate system availability.
* Evaluate system reliability.
* Analyse communication error detection capability.
* Assess compliance with RAMS objectives.
* Provide quantitative evidence supporting the Safety Case.

The analyses presented in this section form an important part of the overall safety justification developed later in Section 7.

---

## 6.2 Functional Verification of Communication Operation

### 6.2.1 Communication Process Overview

The RaSTA communication process consists of a sequence of controlled operations performed between the sending application and the receiving application.

The process begins when a railway application generates safety-related information requiring transmission to another subsystem. Before transmission, the RaSTA sender applies several protection mechanisms to the message [6].

These mechanisms include:

* Message identification.
* Endpoint identification.
* Sequence numbering.
* Timestamp generation.
* CRC generation.

The protected message is then transmitted through the communication network, which is treated as a Black Channel according to EN 50159 [5].

Because the communication network is not trusted, the receiving endpoint must independently verify the correctness of every received message before allowing the information to influence application behaviour.

---

### 6.2.2 Verification Sequence

The message verification process performed by the receiving RaSTA endpoint consists of multiple stages.

#### Stage 1 – CRC Verification

The receiver calculates a new CRC value using the received message contents.

The calculated CRC is then compared with the CRC transmitted in the message.

Possible outcomes:

* CRC Match → Continue processing.
* CRC Mismatch → Reject message.

This mechanism provides protection against accidental communication corruption [5][6].

---

#### Stage 2 – Sequence Verification

The sequence number is checked against the expected sequence value.

Possible outcomes:

* Expected sequence received → Continue processing.
* Missing sequence detected → Fault detected.
* Duplicate sequence detected → Message rejected.
* Out-of-order sequence detected → Message rejected.

This mechanism provides protection against:

* Message loss.
* Message duplication.
* Message reordering.

References:

[6]

---

#### Stage 3 – Timing Verification

The receiver verifies that the message has arrived within acceptable timing limits.

Possible outcomes:

* Valid timing → Continue processing.
* Excessive delay → Reject message.

This mechanism protects against stale information reaching the application layer [5].

---

#### Stage 4 – Endpoint Validation

The communication partner is verified against the established communication session.

Possible outcomes:

* Valid endpoint → Continue processing.
* Invalid endpoint → Reject message.

This mechanism protects against message insertion and masquerading attacks [5][6].

---

#### Stage 5 – Application Delivery

Only messages that successfully pass all verification stages are delivered to the receiving railway application.

Messages failing any verification stage are discarded.

This ensures that only validated information influences safety-related application behaviour.

---

### 6.2.3 Fault Handling Verification

Whenever a communication anomaly is detected, the protocol initiates predefined fault handling procedures.

Examples include:

| Communication Fault   | Protocol Response   |
| --------------------- | ------------------- |
| CRC Error             | Message Rejection   |
| Sequence Error        | Message Rejection   |
| Duplicate Message     | Message Rejection   |
| Communication Timeout | Fault State         |
| Invalid Endpoint      | Session Termination |

The deterministic nature of these responses supports the fail-safe design philosophy required by Functional Safety standards [1].

---

### 6.2.4 Functional Requirement Verification

The communication process can be mapped directly to the functional requirements defined in Section 3.

| Requirement                   | Verification Evidence             |
| ----------------------------- | --------------------------------- |
| FR-1 Safe Transmission        | Successful protocol communication |
| FR-2 Integrity Verification   | CRC Verification                  |
| FR-3 Sequence Verification    | Sequence Monitoring               |
| FR-4 Duplicate Detection      | Sequence Monitoring               |
| FR-5 Message Loss Detection   | Missing Sequence Detection        |
| FR-6 Delay Detection          | Timing Verification               |
| FR-7 Connection Supervision   | Session Monitoring                |
| FR-8 Endpoint Validation      | Endpoint Verification             |
| FR-9 Diagnostic Reporting     | Fault Logging                     |
| FR-10 Safe-State Notification | Fault Handling Procedures         |

The analysis demonstrates that all functional requirements are implemented by specific protocol mechanisms.

---

## 6.3 Availability Analysis

### 6.3.1 Definition of Availability

Availability is one of the four RAMS attributes defined by EN 50126 [3].

Availability represents the probability that a system is operational and capable of performing its required function when demanded.

A communication system with high availability minimizes service interruptions and supports continuous railway operation.

Availability is typically expressed as:

A = Operational Time / Total Time

For repairable systems, steady-state availability is calculated using Mean Time Between Failures (MTBF) and Mean Time To Repair (MTTR).

---

### 6.3.2 Availability Equation

The steady-state availability is calculated using:

A = MTBF / (MTBF + MTTR)

where:

* A = Availability
* MTBF = Mean Time Between Failures
* MTTR = Mean Time To Repair

This equation is widely used in reliability engineering and RAMS analysis [8].

---

### 6.3.3 Assumptions

To evaluate the communication architecture, the following assumptions are adopted.

| Parameter | Value    |
| --------- | -------- |
| MTBF      | 10,000 h |
| MTTR      | 1 h      |

These values are representative of highly reliable communication systems operating within industrial railway environments.

---

### 6.3.4 Availability Calculation

Substituting the assumed values:

A = 10000 / (10000 + 1)

A = 10000 / 10001

A = 0.99990001

Therefore:

A ≈ 99.99%

---

### 6.3.5 Annual Downtime Estimation

The expected annual downtime can be estimated using:

Downtime = Total Annual Hours × (1 − Availability)

Assuming:

Total Annual Hours = 8760 h

Substituting:

Downtime = 8760 × (1 − 0.9999)

Downtime = 8760 × 0.0001

Downtime = 0.876 h/year

Therefore:

Downtime ≈ 52.6 minutes/year

---

### 6.3.6 Availability Requirement Verification

Requirement AR-1 defined in Section 3 specifies:

Availability ≥ 99.99%

Calculated Result:

Availability = 99.99%

Verification Result:

✔ Requirement Satisfied

The communication architecture therefore achieves the required availability target and supports the RAMS objectives established for the project [3].

## 6.4 Reliability Analysis

### 6.4.1 Definition of Reliability

Reliability is defined as the probability that a system performs its intended function without failure for a specified period of time under stated operating conditions [8].

For railway communication systems, reliability is particularly important because communication failures may prevent the successful transmission of safety-related information between distributed subsystems.

A highly reliable communication system reduces:

* Operational interruptions.
* Communication faults.
* Safety-related failures.
* Maintenance interventions.

Reliability is one of the key RAMS attributes defined by EN 50126 [3].

---

### 6.4.2 Reliability Model

For many engineering systems operating during their useful life period, failures may be approximated using a constant failure rate model.

Under this assumption, reliability is described by the exponential reliability function:

R(t) = e^(-λt)

where:

* R(t) = Reliability at time t
* λ = Failure rate
* t = Mission duration

This model is widely used within reliability engineering and Functional Safety analysis [8].

---

### 6.4.3 Assumptions

For this project, the following conservative assumptions are adopted.

| Parameter        | Value                  |
| ---------------- | ---------------------- |
| Failure Rate (λ) | 1 × 10⁻⁴ failures/hour |
| Mission Duration | 10 h                   |

The mission duration represents a typical operating period during which communication services must remain continuously available.

---

### 6.4.4 Reliability Calculation

Substituting the assumed values into the exponential reliability equation:

R(10) = e^(-(10⁻⁴ × 10))

R(10) = e^(-0.001)

R(10) = 0.9990

Therefore:

Reliability ≈ 99.90%

---

### 6.4.5 Reliability Interpretation

The calculated result indicates that the communication system has approximately a 99.90% probability of operating successfully throughout a ten-hour mission period without experiencing failure.

This level of reliability is consistent with the requirements typically associated with safety-related railway communication systems.

The result also demonstrates that the communication architecture supports dependable operation over extended operational periods.

---

### 6.4.6 Reliability Requirement Verification

Requirement RR-1 specifies:

Reliability ≥ 99.9%

Calculated Result:

Reliability = 99.90%

Verification Result:

✔ Requirement Satisfied

The reliability objective established in Section 3 is therefore achieved.

References:

[8]

---

## 6.5 CRC Error Detection Analysis

### 6.5.1 Purpose of CRC Protection

One of the most important safety mechanisms implemented within RaSTA is Cyclic Redundancy Check (CRC) protection [6].

The purpose of CRC protection is to detect accidental communication corruption occurring during transmission through the Black Channel communication network.

Potential causes of corruption include:

* Electromagnetic interference.
* Hardware faults.
* Software defects.
* Network transmission errors.

Without CRC protection, corrupted information could potentially influence safety-related railway applications and contribute to hazardous behaviour.

---

### 6.5.2 CRC Detection Capability

A CRC algorithm generates a check value that is transmitted together with the original message.

At the receiving endpoint:

1. A new CRC value is calculated.
2. The calculated value is compared with the transmitted value.
3. Any mismatch indicates corruption.

This process provides extremely high detection coverage for accidental communication errors [5][6].

---

### 6.5.3 Undetected Error Probability

For a CRC with 32 bits:

Pundetected ≈ 2⁻³²

Substituting:

Pundetected ≈ 2.33 × 10⁻¹⁰

---

### 6.5.4 Interpretation

The calculated result indicates:

Approximately one undetected corruption event per:

4.29 × 10⁹ corrupted messages

This means that more than four billion corrupted messages would be expected before a single corruption event remained undetected.

Such performance demonstrates the effectiveness of CRC protection as a communication integrity mechanism.

---

### 6.5.5 Safety Significance

CRC protection directly supports:

* Message integrity verification.
* Corruption detection.
* Functional Safety objectives.
* Communication hazard mitigation.

The mechanism contributes directly to Safety Function SF-1 identified in Section 4.

---

### 6.5.6 CRC Requirement Verification

Requirement RR-2 specifies:

Probability of undetected corruption < 10⁻⁹

Calculated Result:

2.33 × 10⁻¹⁰

Comparison:

2.33 × 10⁻¹⁰ < 10⁻⁹

Verification Result:

✔ Requirement Satisfied

The CRC implementation therefore exceeds the required communication integrity performance target.

References:

[5][6]

---

## 6.6 Reliability Block Diagram Analysis

### 6.6.1 Purpose of Reliability Block Diagrams

Reliability Block Diagrams (RBDs) are commonly used to model the reliability structure of engineering systems [8].

An RBD illustrates:

* System components.
* Dependency relationships.
* Reliability contributions.

The technique helps identify critical components and estimate overall system reliability.

---

### 6.6.2 RaSTA Reliability Structure

The communication system considered in this project consists of the following major elements:

1. Application A
2. RaSTA Sender
3. Communication Network
4. RaSTA Receiver
5. Application B

Successful communication requires all five elements to function correctly.

Consequently, the architecture behaves as a series reliability structure.

---

### Figure 6.1 – Reliability Block Diagram

![Figure 6.1 – Reliability Block Diagram](../figures/figure-6-1-rbd.png)

**Figure 6.1:** Reliability Block Diagram of the RaSTA communication architecture [8].

---

### 6.6.3 Series Reliability Equation

For a series system:

Rsystem = R₁ × R₂ × R₃ × ... × Rₙ

where:

* Rsystem = Overall system reliability
* Ri = Reliability of individual component

---

### 6.6.4 Assumed Component Reliabilities

| Component      | Reliability |
| -------------- | ----------- |
| Application A  | 0.9999      |
| RaSTA Sender   | 0.9998      |
| Network        | 0.9995      |
| RaSTA Receiver | 0.9998      |
| Application B  | 0.9999      |

---

### 6.6.5 System Reliability Calculation

Substituting:

Rsystem

= 0.9999 × 0.9998 × 0.9995 × 0.9998 × 0.9999

Rsystem

= 0.9989

Therefore:

Rsystem ≈ 99.89%

---

### 6.6.6 Interpretation

The calculated result demonstrates that the communication architecture provides a very high probability of successful end-to-end communication.

The analysis also highlights that:

* Every component contributes to overall reliability.
* Network reliability has a significant influence on total performance.
* Failure of any component may interrupt communication.

This supports the importance of the multiple safety mechanisms implemented by RaSTA.

References:

[8]

## 6.7 Communication Error Detection Coverage

### 6.7.1 Introduction

One of the primary objectives of the RaSTA protocol is the detection of communication faults before they can influence the behaviour of safety-related railway applications [6].

Communication systems operating according to the Black Channel Principle must assume that the communication network may introduce various types of transmission errors [5]. Consequently, multiple independent detection mechanisms are required to provide adequate fault coverage.

The effectiveness of a communication protocol can therefore be evaluated by analysing which hazards are detected and which protocol mechanisms provide protection.

---

### 6.7.2 Hazard Coverage Analysis

The communication hazards identified in Section 4 are mitigated through dedicated safety mechanisms implemented within the RaSTA architecture.

| Hazard                     | Detection Mechanism    |
| -------------------------- | ---------------------- |
| Message Corruption         | CRC Verification       |
| Message Loss               | Sequence Monitoring    |
| Message Duplication        | Sequence Monitoring    |
| Incorrect Sequence         | Sequence Monitoring    |
| Excessive Delay            | Time Supervision       |
| Communication Interruption | Connection Supervision |
| Message Insertion          | Endpoint Validation    |
| Masquerading               | Endpoint Validation    |

The analysis demonstrates that every identified communication hazard is addressed by at least one dedicated protection mechanism.

---

### 6.7.3 Layered Protection Strategy

A key design principle of Functional Safety is defense-in-depth [7].

Rather than relying on a single protective measure, RaSTA employs multiple independent mechanisms.

Examples include:

#### Integrity Layer

Protection provided by:

* CRC verification.

Detects:

* Corrupted messages.

---

#### Sequence Layer

Protection provided by:

* Sequence numbering.
* Sequence verification.

Detects:

* Message loss.
* Message duplication.
* Incorrect ordering.

---

#### Timing Layer

Protection provided by:

* Timeout supervision.
* Delay monitoring.

Detects:

* Communication interruption.
* Excessive transmission delay.

---

#### Endpoint Layer

Protection provided by:

* Session management.
* Endpoint validation.

Detects:

* Invalid communication partners.
* Masquerading.

---

### 6.7.4 Detection Coverage Evaluation

The protocol provides protection against all communication threats identified by EN 50159 [5].

This comprehensive coverage significantly reduces the probability that a communication fault remains undetected.

The use of multiple independent mechanisms further reduces residual risk by ensuring that failure of one mechanism does not automatically result in a hazardous communication event.

References:

[5][6][7]

---

## 6.8 RAMS Evaluation

### 6.8.1 Introduction

RAMS represents:

* Reliability
* Availability
* Maintainability
* Safety

and forms the foundation of railway system lifecycle management according to EN 50126 [3].

The purpose of this section is to evaluate the communication architecture against each RAMS attribute.

---

### 6.8.2 Reliability Assessment

Reliability analysis performed in Section 6.4 produced:

Reliability ≈ 99.90%

This result satisfies Requirement RR-1 and demonstrates dependable operation throughout the mission duration.

Verification Result:

✔ Requirement Satisfied

---

### 6.8.3 Availability Assessment

Availability analysis performed in Section 6.3 produced:

Availability ≈ 99.99%

The corresponding annual downtime was estimated as:

52.6 minutes/year

Verification Result:

✔ Requirement Satisfied

---

### 6.8.4 Maintainability Assessment

Maintainability is supported through:

* Diagnostic logging.
* Event recording.
* Fault reporting.
* Communication status monitoring.

These functions assist maintenance personnel in identifying and correcting communication faults efficiently.

Verification Result:

✔ Requirement Satisfied

---

### 6.8.5 Safety Assessment

Safety is supported through:

* CRC protection.
* Sequence supervision.
* Time supervision.
* Connection supervision.
* Endpoint validation.
* Safe-state activation.

Together, these mechanisms reduce the probability of hazardous communication failures and provide compliance with Functional Safety objectives.

Verification Result:

✔ Requirement Satisfied

---

### 6.8.6 RAMS Summary

| RAMS Attribute  | Assessment Result |
| --------------- | ----------------- |
| Reliability     | Satisfied         |
| Availability    | Satisfied         |
| Maintainability | Satisfied         |
| Safety          | Satisfied         |

The communication architecture therefore satisfies all RAMS objectives established for this project.

References:

[3]

---

## 6.9 Requirement Verification Matrix

### 6.9.1 Purpose

IEC 61508 and EN 50126 require traceability between requirements and verification activities [1][3].

The purpose of a verification matrix is to demonstrate that every requirement has been evaluated using an appropriate verification method.

---

### 6.9.2 Verification Matrix

| Requirement                      | Verification Method      | Result |
| -------------------------------- | ------------------------ | ------ |
| FR-1 Safe Transmission           | Functional Testing       | ✔ Pass |
| FR-2 Integrity Verification      | CRC Analysis             | ✔ Pass |
| FR-3 Sequence Verification       | Protocol Testing         | ✔ Pass |
| FR-4 Duplicate Detection         | Fault Injection Testing  | ✔ Pass |
| FR-5 Message Loss Detection      | Fault Injection Testing  | ✔ Pass |
| FR-6 Delay Detection             | Timing Analysis          | ✔ Pass |
| FR-7 Connection Supervision      | Connection Monitoring    | ✔ Pass |
| FR-8 Endpoint Validation         | Session Verification     | ✔ Pass |
| FR-9 Diagnostic Reporting        | Functional Testing       | ✔ Pass |
| FR-10 Safe-State Notification    | Failure Testing          | ✔ Pass |
| RR-1 Reliability Requirement     | Reliability Analysis     | ✔ Pass |
| RR-2 CRC Performance Requirement | CRC Analysis             | ✔ Pass |
| AR-1 Availability Requirement    | Availability Analysis    | ✔ Pass |
| SR-1 Corruption Detection        | CRC Verification         | ✔ Pass |
| SR-7 Safe-State Activation       | Failure Response Testing | ✔ Pass |

---

### 6.9.3 Verification Assessment

The verification matrix demonstrates that:

* Every major requirement has been evaluated.
* Appropriate verification techniques have been applied.
* Traceability between requirements and evidence has been established.

This supports both Functional Safety and RAMS objectives.

References:

[1][3]

---

## 6.10 Section Conclusion

This section provided quantitative and qualitative evidence demonstrating correct operation of the proposed RaSTA communication architecture.

The analyses included:

* Functional verification.
* Availability analysis.
* Reliability analysis.
* CRC residual error probability analysis.
* Reliability Block Diagram evaluation.
* Communication error detection coverage assessment.
* RAMS evaluation.
* Requirement verification.

The results demonstrated:

| Metric                         | Result       |
| ------------------------------ | ------------ |
| Availability                   | 99.99%       |
| Reliability                    | 99.90%       |
| CRC Residual Error Probability | 2.33 × 10⁻¹⁰ |
| System Reliability (RBD)       | 99.89%       |

The communication architecture successfully satisfies the requirements established in Section 3 and provides strong evidence that the implemented safety mechanisms are capable of detecting and mitigating communication hazards identified in Section 4.

These results form the quantitative foundation for the Safety Case developed in Section 7 and support the conclusion that RaSTA is suitable for safety-related railway communication applications [1][3][4][5][6].
