# 7. Safety Case

## 7.1 Introduction

The purpose of this safety case is to demonstrate that the Rail Safe Transport Application (RaSTA) provides communication services that satisfy the safety objectives defined for railway signalling systems.

RaSTA was specifically developed to provide safe communication in accordance with DIN EN 50159 and to support safety-related railway applications operating under the RAMS framework defined by EN 50126, EN 50128, and EN 50129 [2][3][4][5].

The fundamental safety principle of RaSTA is:

> Unsafe communication shall never be accepted by the receiving application.

Whenever communication validity cannot be guaranteed, the protocol transitions to a predefined safe state by terminating the connection.

---

## 7.2 Safety Objectives

The primary safety objectives of RaSTA are:

1. Detect message corruption.
2. Detect delayed messages.
3. Detect lost messages.
4. Detect repeated messages.
5. Detect message resequencing.
6. Detect unauthorized communication.
7. Maintain communication availability.
8. Prevent unsafe information from reaching the application.

These objectives directly address the communication threats identified by DIN EN 50159 [2].

---

## 7.3 DIN EN 50159 Communication Threat Analysis

DIN EN 50159 identifies several communication threats that must be controlled by safety-related communication systems.

| Threat       | Description                              |
| ------------ | ---------------------------------------- |
| Corruption   | Modification of message contents         |
| Repetition   | Repeated transmission of a valid message |
| Deletion     | Loss of a message                        |
| Delay        | Excessive transmission delay             |
| Insertion    | Unauthorized message creation            |
| Resequencing | Incorrect message order                  |
| Masquerade   | Sender impersonation                     |

**Table 7-1:** Communication threats defined by DIN EN 50159 [2].

These threats form the basis of the RaSTA safety architecture.

---

## 7.4 Hazard Identification

The communication threats can be translated into system-level hazards.

| Hazard ID | Hazard Description               | Possible Consequence            |
| --------- | -------------------------------- | ------------------------------- |
| H-1       | Corrupted command accepted       | Incorrect signalling action     |
| H-2       | Delayed information accepted     | Decision based on obsolete data |
| H-3       | Lost command undetected          | Missing safety function         |
| H-4       | Duplicate command accepted       | Repeated action execution       |
| H-5       | Unauthorized sender accepted     | Unsafe system operation         |
| H-6       | Message order violation          | Inconsistent application state  |
| H-7       | Communication failure undetected | Loss of situational awareness   |

**Table 7-2:** Hazard identification for RaSTA communication.

---

## 7.5 Risk Assessment

The identified hazards were assessed qualitatively using the railway RAMS approach.

![Risk Matrix](../figures/figure-4-1-risk-matrix.png)

**Figure 7-1:** Risk matrix used during hazard evaluation.

Without mitigation, several communication hazards could potentially lead to unsafe railway operation.

Therefore dedicated safety mechanisms are required.

---

## 7.6 Safety Mechanisms Implemented by RaSTA

RaSTA employs multiple independent safety mechanisms.

| Safety Mechanism            | Purpose                    |
| --------------------------- | -------------------------- |
| MD4 Safety Code             | Integrity verification     |
| Sender ID Validation        | Source authentication      |
| Receiver ID Validation      | Destination verification   |
| Sequence Number Supervision | Ordering verification      |
| Confirmed Sequence Numbers  | Delivery verification      |
| Timestamp Monitoring        | Timeliness verification    |
| Heartbeat Messages          | Availability monitoring    |
| Retransmission Mechanism    | Recovery from message loss |
| Redundancy Layer            | Availability improvement   |

**Table 7-3:** Principal RaSTA safety mechanisms.

The use of multiple independent mechanisms increases fault detection coverage.

---

## 7.7 Threat-to-Mechanism Mapping

The relationship between DIN EN 50159 threats and RaSTA protections is shown below.

| EN 50159 Threat | RaSTA Protection Mechanism                |
| --------------- | ----------------------------------------- |
| Corruption      | MD4 Safety Code                           |
| Repetition      | Sequence Number Verification              |
| Deletion        | Retransmission Procedure                  |
| Delay           | Adaptive Timeout Monitoring               |
| Insertion       | Sender and Receiver Validation            |
| Resequencing    | Sequence Number Verification              |
| Masquerade      | Sender and Receiver Identifier Validation |

**Table 7-4:** Mapping between communication threats and RaSTA mechanisms.

This mapping demonstrates that all principal EN 50159 communication threats are addressed.

---

## 7.8 Integrity Safety Argument

Message corruption is controlled using the safety code field.

The sender calculates an MD4 value over the complete protocol data unit.

Upon reception:

```text
Receive Message
       ↓
Calculate MD4
       ↓
Compare Safety Code
       ↓
Accept or Reject
```

**Figure 7-2:** Integrity verification process.

If the calculated value differs from the received value:

```text
Safety Code Error
        ↓
Message Discarded
```

Therefore corrupted information cannot be delivered to the application [1][9].

---

## 7.9 Sequence Integrity Safety Argument

Sequence numbers provide protection against:

* Repetition
* Replay
* Deletion
* Resequencing
* Message insertion

Example:

```text
Expected SN = 25
Received SN = 26
```

Result:

```text
Sequence Error
```

The receiver immediately initiates:

```text
RetrReq
```

This prevents missing or reordered messages from being accepted.

---

## 7.10 Timeliness Safety Argument

Correct information may become unsafe if delivered too late.

RaSTA therefore continuously monitors communication timing.

Adaptive supervision uses:

```text
Ti = Trtd + Talive + Tmax
```

If timeout Ti expires:

```text
DiscReq
       ↓
Closed State
```

The connection is terminated before obsolete information can be accepted.

This directly addresses the delay threat identified in DIN EN 50159 [2].

---

## 7.11 Availability Safety Argument

Availability is increased through the Redundancy Layer.

The same message is transmitted on multiple transport channels.

Example:

```text
      Message
         |
 -------------------
 |        |        |
A        B        C
```

If one channel fails:

```text
Channel A = Failed
Channel B = Operational
Channel C = Operational
```

Communication may continue using the remaining channels.

This reduces the probability of communication interruption.

---

## 7.12 Fault Tree Analysis

The top-level hazardous event considered is:

```text
Unsafe Railway Communication
```

The corresponding fault tree is shown below.

![Fault Tree Analysis](../figures/figure-7-2-fta.png)

**Figure 7-3:** Fault tree for unsafe railway communication.

The top event may result from:

* Corrupted communication
* Delayed communication
* Lost communication
* Unauthorized communication

RaSTA interrupts these fault paths through independent detection mechanisms.

---

## 7.13 Failure Handling and Safe State

When communication validity cannot be guaranteed, RaSTA enters a safe state.

Typical triggers include:

* Timeout expiration
* Invalid safety code
* Protocol version mismatch
* Retransmission failure
* Protocol sequence violation

The protocol generates:

```text
DiscReq
```

and transitions to:

```text
Closed
```

No further application data is accepted.

This behaviour is consistent with fail-safe design principles defined by IEC 61508 [11][12][13].

---

## 7.14 Residual Risks

Although RaSTA significantly reduces communication risk, some residual risks remain.

### Complete Network Failure

Simultaneous failure of all transport channels may prevent communication.

Mitigation:

* Timeout detection
* Safe connection termination

### Configuration Errors

Incorrect configuration of:

* Sender identifiers
* Receiver identifiers
* Timing parameters
* MD4 initialization values

may prevent correct operation.

Mitigation:

* Verification and validation activities
* Configuration management

### Application-Level Errors

RaSTA validates communication but not application semantics.

Mitigation:

* Application-level safety analysis
* Independent software verification

---

## 7.15 Safety Claim

### Claim

RaSTA provides safe communication suitable for railway signalling applications.

### Evidence

The protocol implements:

* Integrity verification
* Sequence supervision
* Timeliness monitoring
* Heartbeat supervision
* Retransmission procedures
* Redundancy mechanisms
* Safe failure behaviour

### Verification

The mechanisms were verified through:

* Protocol analysis
* State-machine analysis
* Connection-establishment analysis
* Retransmission scenarios
* Fault Tree Analysis

### Conclusion

Communication failures are either corrected or detected before they can influence the application.

---

## 7.16 Safety Conclusion

The analysis demonstrates that RaSTA provides a comprehensive set of safety mechanisms addressing the communication threats identified by DIN EN 50159.

The protocol detects:

* Corruption
* Delay
* Message loss
* Message repetition
* Message resequencing
* Unauthorized communication

Whenever safe communication can no longer be guaranteed, RaSTA transitions to a safe state by terminating the connection.

Therefore, RaSTA provides a suitable communication framework for safety-related railway signalling systems and satisfies the safety objectives defined by DIN EN 50159 [1][2].
