# 3 System Requirements

## 3.1 Introduction

According to the RAMS lifecycle defined in EN 50126, system requirements provide the foundation for the design, implementation, verification, and validation of safety-related railway systems [3]. Requirements must be complete, consistent, traceable, verifiable, and measurable throughout the system lifecycle [1][3].

For safety-related communication systems, requirements are particularly important because communication failures may directly affect the behaviour of railway control applications [4][5]. The requirements defined in this section establish the expected functionality, performance, reliability, availability, maintainability, and safety characteristics of the RaSTA-based communication system.

The purpose of the communication system is to enable safe transmission of application data between railway subsystems while ensuring that communication failures cannot result in hazardous system behaviour [5][6].

---

## 3.2 Stakeholder Requirements

### Railway Operator

The railway operator requires:

* High communication availability
* Reliable transmission of operational information
* Minimal service interruptions
* Predictable system behaviour during failures [3]

### Safety Authority

The safety authority requires:

* Demonstration of compliance with railway safety standards
* Traceable safety requirements
* Evidence that communication failures are adequately controlled [4]

### System Maintainer

The maintainer requires:

* Diagnostic information
* Event logging
* Fault reporting
* Support for maintenance and troubleshooting activities [3]

### Application Developer

The application developer requires:

* Safe communication services
* Deterministic protocol behaviour
* Protection against communication hazards
* Well-defined protocol interfaces [6]

---

## 3.3 Functional Requirements

### FR-1 Safe Message Transmission

The communication system shall transmit safety-related application data between communicating railway applications [6].

**Verification Method:** Functional Testing

---

### FR-2 Message Integrity Verification

The receiving endpoint shall verify message integrity before delivering data to the application layer [5][6].

**Verification Method:** Protocol Verification Testing

---

### FR-3 Sequence Verification

The receiving endpoint shall verify that messages arrive in the correct order [6].

**Verification Method:** Sequence Fault Injection Testing

---

### FR-4 Duplicate Message Detection

The communication system shall detect and reject duplicated messages [5][6].

**Verification Method:** Communication Fault Testing

---

### FR-5 Message Loss Detection

The communication system shall detect missing messages through sequence monitoring and timeout supervision [5].

**Verification Method:** Communication Interruption Testing

---

### FR-6 Delay Detection

The communication system shall detect messages exceeding the maximum allowable transmission delay [5][6].

**Verification Method:** Timing Verification

---

### FR-7 Connection Supervision

The communication system shall continuously supervise the communication connection during operation [6].

**Verification Method:** Connection Monitoring Tests

---

### FR-8 Endpoint Validation

The communication system shall validate communication partners before exchanging operational data [5][6].

**Verification Method:** Connection Establishment Testing

---

### FR-9 Diagnostic Reporting

Communication failures shall generate diagnostic information accessible to maintenance personnel [3].

**Verification Method:** Diagnostic Function Testing

---

### FR-10 Safe-State Notification

The communication system shall notify the application whenever communication validity can no longer be guaranteed [1][4].

**Verification Method:** Failure Response Testing

---

## 3.4 Performance Requirements

### PR-1 Maximum End-to-End Latency

The communication system shall support transmission of safety-related messages with a maximum latency of:

```text
≤ 100 ms
```

Justification:

Safety-related information must remain valid when received by the application [5].

---

### PR-2 Communication Fault Detection Time

Communication interruptions shall be detected within:

```text
≤ 500 ms
```

Justification:

Rapid detection reduces exposure to unsafe communication states [4][5].

---

### PR-3 Connection Establishment Time

A communication connection shall be established within:

```text
≤ 2 seconds
```

following initialization.

---

## 3.5 Reliability Requirements

Reliability is defined as the probability that a system performs its intended function without failure for a specified period of time [3][8].

### RR-1 Mission Reliability

The communication system shall achieve:

```text
Reliability ≥ 99.9%
```

for a mission duration of 10 hours.

**Verification Method:** Reliability Analysis

---

### RR-2 Error Detection Reliability

The probability of failing to detect a communication corruption event shall be less than:

```text
10^-9 per message
```

**Verification Method:** CRC Analysis

---

### RR-3 Communication Supervision Reliability

Communication supervision functions shall detect communication interruptions with high confidence.

**Verification Method:** Fault Injection Testing

---

## 3.6 Availability Requirements

Availability is defined as the probability that a system is operational when required [3].

### AR-1 Operational Availability

The communication service shall achieve:

```text
Availability ≥ 99.99%
```

**Verification Method:** Availability Analysis

---

### AR-2 Service Continuity

Temporary communication disturbances shall not result in permanent communication failure.

**Verification Method:** Recovery Testing

---

## 3.7 Maintainability Requirements

### MR-1 Diagnostic Logging

All communication failures shall be logged.

**Verification Method:** Diagnostic Testing

---

### MR-2 Fault Identification

Diagnostic information shall support identification of communication failure causes.

**Verification Method:** Maintenance Review

---

### MR-3 Event History

The system shall maintain a history of communication events and failures.

**Verification Method:** Log Analysis

---

## 3.8 Safety Requirements

### SR-1 Corrupted Message Rejection

Corrupted messages shall be detected and rejected before delivery to the application [5][6].

---

### SR-2 Duplicate Message Rejection

Duplicated messages shall not be delivered to the application [5][6].

---

### SR-3 Sequence Error Detection

Out-of-order messages shall be detected and rejected [6].

---

### SR-4 Delay Error Detection

Messages exceeding timing limits shall not be accepted [5][6].

---

### SR-5 Communication Interruption Detection

Communication interruptions shall trigger fault handling procedures [5].

---

### SR-6 Invalid Endpoint Detection

Communication with unauthorized endpoints shall be prevented [5][6].

---

### SR-7 Safe-State Activation

Whenever communication safety cannot be guaranteed, the application shall enter a predefined safe state [1][4].

---

## 3.9 RAMS Requirements Summary

| RAMS Category   | Key Requirement                                   |
| --------------- | ------------------------------------------------- |
| Reliability     | Reliability ≥ 99.9%                               |
| Availability    | Availability ≥ 99.99%                             |
| Maintainability | Diagnostic logging and fault identification       |
| Safety          | Detection and mitigation of communication hazards |

These RAMS requirements provide measurable targets for evaluating the effectiveness of the RaSTA communication architecture [3].

---

## 3.10 Requirements Traceability Foundation

The requirements established in this section form the basis for all subsequent engineering activities.

```text
Requirements
      ↓
Hazards
      ↓
Safety Functions
      ↓
Architecture
      ↓
Verification
      ↓
Safety Case
```

This traceability approach ensures that every architectural component and safety mechanism introduced later in the report can be directly justified by a corresponding requirement and safety objective [1][3][4].
