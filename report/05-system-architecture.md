# 5 System Architecture

## 5.1 Introduction

The purpose of the system architecture is to implement the safety functions derived from the hazard analysis presented in Section 4. According to IEC 61508 and EN 50126, architectural design must demonstrate how identified hazards are mitigated through concrete system components and mechanisms [1][3].

RaSTA is designed as a safety-related communication protocol operating according to the Black Channel Principle defined by EN 50159 [5]. The protocol allows safety-related railway applications to communicate safely over communication networks that are not themselves considered safety-related.

The architecture therefore focuses on implementing safety mechanisms at the communication endpoints rather than relying on the underlying communication infrastructure [5][6].

---

## 5.2 Architectural Design Principles

### Fail-Safe Principle

Whenever communication validity cannot be guaranteed, the protocol must transition the application into a predefined safe state [1][4].

This principle ensures that uncertain information never influences safety-related decisions.

---

### Defense-in-Depth

Safety is achieved through multiple independent protection mechanisms rather than a single protective measure [7].

Examples include:

* CRC protection
* Sequence supervision
* Time supervision
* Connection supervision
* Endpoint validation

The failure of one mechanism should not immediately result in a hazardous condition.

---

### Black Channel Principle

The communication network is treated as an untrusted transmission medium [5].

The network may:

* Corrupt messages
* Delay messages
* Lose messages
* Duplicate messages
* Reorder messages

The responsibility for safety therefore resides entirely within the RaSTA endpoints.

---

### Traceability Principle

Every architectural component must be traceable to one or more safety requirements and safety functions [1][4].

This traceability supports verification, validation, and safety assessment activities.

---

## 5.3 Overall System Architecture

The communication architecture considered in this project consists of:

### Application A

The transmitting railway application.

Examples:

* Interlocking system
* Object controller
* Train control system

Responsibilities:

* Generate operational data
* Generate safety-related commands

---

### RaSTA Sender

Responsible for:

* Message preparation
* Sequence numbering
* CRC generation
* Connection supervision

---

### Communication Network

The Black Channel.

Examples:

* Ethernet
* IP network
* UDP communication

Safety properties are not assumed.

---

### RaSTA Receiver

Responsible for:

* CRC verification
* Sequence verification
* Delay detection
* Connection monitoring

---

### Application B

The receiving railway application.

Responsibilities:

* Execute validated commands
* Enter safe state when required

---

## Figure 5.1 – RaSTA Architecture

![Figure 5.1 – RaSTA Architecture](../figures/figure-5-1-rasta-architecture.png)

**Figure 5.1:** RaSTA communication architecture based on the Black Channel Principle [5][6].

---

## 5.4 RaSTA Communication State Model

Communication between two RaSTA nodes follows a controlled state machine [6].

### State S0 – Disconnected

Characteristics:

* No valid communication session
* No safety-related information exchange

---

### State S1 – Connection Establishment

Characteristics:

* Endpoint identification
* Session initialization
* Synchronization

Purpose:

Ensure both communication partners are valid and synchronized.

---

### State S2 – Operational

Characteristics:

* Safe communication active
* Message exchange permitted

Safety mechanisms active:

* CRC supervision
* Sequence monitoring
* Timeout monitoring

---

### State S3 – Fault State

Characteristics:

* Communication anomaly detected

Possible causes:

* Invalid CRC
* Missing messages
* Connection timeout
* Sequence violation

Result:

Fault handling initiated.

---

### State S4 – Recovery

Characteristics:

* Re-establishment of communication
* Re-synchronization of protocol states

Transition:

Recovery → Operational only if communication integrity is restored.

---

## Figure 5.2 – RaSTA State Machine

![Figure 5.2 – RaSTA State Machine](../figures/figure-5-2-state-machine.png)

**Figure 5.2:** Simplified RaSTA communication state model [6].

---

## 5.5 Message Structure

The RaSTA protocol uses structured messages containing both application data and safety information [6].

For this project, the following simplified frame structure is considered.

| Field           | Size (bits) |
| --------------- | ----------- |
| Message Type    | 8           |
| Sender ID       | 16          |
| Receiver ID     | 16          |
| Sequence Number | 32          |
| Timestamp       | 32          |
| Payload         | 128         |
| CRC             | 32          |

### Total Message Size

```text
Frame Size =
8 + 16 + 16 + 32 + 32 + 128 + 32
```

```text
Frame Size = 264 bits
```

Therefore:

**Total Frame Size = 264 bits**

This information is useful for transmission and reliability calculations performed later in the report.

---

## 5.6 Safety Mechanisms Implemented by the Architecture

### CRC Protection

**Purpose:** Detect communication corruption

**Safety Function:** SF-1

---

### Sequence Monitoring

**Purpose:** Detect:

* Message loss
* Message duplication
* Incorrect ordering

**Safety Function:** SF-2

---

### Timeout Supervision

**Purpose:** Detect communication interruption

**Safety Function:** SF-3

---

### Connection Supervision

**Purpose:** Detect invalid communication states

**Safety Function:** SF-4

---

### Endpoint Validation

**Purpose:** Prevent communication with unauthorized endpoints

**Safety Function:** SF-5

---

### Safe-State Activation

**Purpose:** Ensure fail-safe operation

**Safety Function:** SF-6

---

## 5.7 Architectural Traceability Matrix

| Safety Function             | Architectural Component     |
| --------------------------- | --------------------------- |
| SF-1 Integrity Protection   | CRC Module                  |
| SF-2 Sequence Supervision   | Sequence Counter Manager    |
| SF-3 Time Supervision       | Timeout Monitor             |
| SF-4 Connection Supervision | Connection Manager          |
| SF-5 Endpoint Validation    | Session Establishment Logic |
| SF-6 Safe-State Activation  | Safety Controller           |

This traceability demonstrates that every safety function identified in Section 4 has a corresponding implementation element within the architecture.

---

## 5.8 Architectural Evaluation

The proposed architecture satisfies the design principles established at the beginning of this section.

### Hazard Coverage

Every identified communication hazard is addressed by at least one dedicated safety mechanism.

### Layered Protection

Multiple independent protection layers reduce the probability of undetected communication failures.

### Compliance with Functional Safety Principles

The architecture supports:

* Hazard mitigation
* Risk reduction
* Fail-safe operation
* Verification and validation
* Safety case development

These characteristics are consistent with the expectations of IEC 61508, EN 50126, EN 50129, and EN 50159 [1][3][4][5].

---

## 5.9 Section Conclusion

The RaSTA architecture provides a structured approach to safety-related communication by implementing safety mechanisms at the communication endpoints while treating the transmission network as a Black Channel [5][6].

The architecture directly implements the safety functions derived from the hazard analysis and provides the foundation for the quantitative reliability, availability, and safety analyses presented in the following sections.

Furthermore, the architectural traceability established in this section ensures that all safety requirements can be linked to specific implementation elements, supporting verification, validation, and safety assessment activities throughout the system lifecycle [1][3][4].
