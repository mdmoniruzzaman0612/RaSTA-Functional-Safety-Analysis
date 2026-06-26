# 3. System Requirements

## 3.1 Introduction

The behaviour of the Rail Safe Transport Application (RaSTA) is defined by the requirements specified in **DIN VDE V 0831-200**. Rather than prescribing a particular implementation, the standard specifies the communication behaviour, message formats, protocol states, and safety mechanisms that every compatible implementation shall follow.

The primary objective of these requirements is to ensure that safety-related railway communication remains correct, timely, and available even when the underlying transport network cannot guarantee reliable communication.

The requirements presented in this section form the basis for the protocol architecture analysed in the subsequent chapters.

---

## 3.2 Functional Requirements

The RaSTA protocol shall provide the following core communication services.

| ID    | Functional Requirement                                                             |
| ----- | ---------------------------------------------------------------------------------- |
| FR-1  | Establish a safe communication connection between two RaSTA instances.             |
| FR-2  | Securely transmit application messages.                                            |
| FR-3  | Receive and validate protocol messages.                                            |
| FR-4  | Detect corrupted messages before delivery to the application.                      |
| FR-5  | Detect missing or duplicated messages.                                             |
| FR-6  | Support retransmission of lost messages.                                           |
| FR-7  | Continuously supervise communication timing.                                       |
| FR-8  | Generate heartbeat messages during communication inactivity.                       |
| FR-9  | Maintain communication availability using redundant transport channels.            |
| FR-10 | Safely terminate communication whenever protocol correctness cannot be guaranteed. |

**Table 3-1.** Primary functional requirements derived from DIN VDE V 0831-200.

---

## 3.3 Safety Requirements

The protocol is designed to satisfy the communication safety principles defined by **DIN EN 50159**.

Accordingly, RaSTA shall detect the following communication threats before information reaches the receiving application.

| ID   | Safety Requirement                                                | Implemented Mechanism              |
| ---- | ----------------------------------------------------------------- | ---------------------------------- |
| SR-1 | Detect message corruption                                         | MD4 Safety Code                    |
| SR-2 | Detect message repetition                                         | Sequence Numbers                   |
| SR-3 | Detect message deletion                                           | Retransmission Procedure           |
| SR-4 | Detect excessive delay                                            | Adaptive Time Monitoring           |
| SR-5 | Detect incorrect message ordering                                 | Sequence Supervision               |
| SR-6 | Detect unauthorized communication                                 | Sender and Receiver Identification |
| SR-7 | Enter a safe state if communication validity cannot be guaranteed | Disconnect Procedure               |

**Table 3-2.** Functional safety requirements.

These requirements directly address the communication threats identified in DIN EN 50159.

---

## 3.4 Availability Requirements

Unlike many communication protocols, RaSTA is required to provide both communication safety and high availability.

The protocol therefore implements a dedicated Redundancy Layer whose responsibilities include:

* Simultaneous transmission over multiple transport channels.
* Duplicate suppression.
* Message reordering.
* CRC verification.
* Transport-channel diagnostics.

The use of redundant transport channels allows communication to continue even if one transport channel becomes unavailable.

---

## 3.5 Communication Requirements

The specification defines several communication services that every RaSTA implementation shall support.

These include:

* Opening a communication connection.
* Closing a communication connection.
* Sending application data.
* Receiving application data.
* Querying connection status.
* Receiving protocol notifications.
* Receiving diagnostic information.

The corresponding protocol services are realised through the Safety and Retransmission Layer.

---

## 3.6 Protocol Compatibility Requirements

One important design objective of RaSTA is interoperability.

For this reason, the standard distinguishes between:

### Compatibility-Relevant Requirements

These requirements shall be implemented exactly as specified.

They include:

* Protocol message formats.
* Protocol state transitions.
* Message processing rules.
* Sequence-number handling.
* Timing supervision.
* Connection establishment procedures.

Failure to implement these requirements would prevent interoperability between independent RaSTA implementations.

### Non-Compatibility-Relevant Requirements

These requirements describe recommended implementation behaviour.

Typical examples include:

* Application programming interfaces.
* Diagnostic interfaces.
* Transport adaptation interfaces.

Different implementations may realise these interfaces differently while remaining protocol-compatible.

---

## 3.7 Performance Requirements

The RaSTA specification places timing requirements on communication rather than fixed network performance requirements.

Important configurable timing parameters include:

| Parameter  | Description                               |
| ---------- | ----------------------------------------- |
| **Tmax**   | Maximum acceptable message age            |
| **Th**     | Heartbeat transmission interval           |
| **Ti**     | Adaptive supervision timeout              |
| **Trtd**   | Measured round-trip delay                 |
| **Talive** | Connection supervision delay              |
| **Tseq**   | Waiting time for out-of-sequence messages |

**Table 3-3.** Principal timing parameters.

Rather than assuming deterministic network delays, RaSTA continuously adapts its timeout calculations according to measured communication behaviour.

---

## 3.8 Configuration Requirements

Several protocol parameters must be configured before communication begins.

Examples include:

* RaSTA Network Identifier
* Sender Identifier
* Receiver Identifier
* MD4 Initial Value
* Number of Transport Channels
* CRC Type
* Receive Buffer Size
* Packetisation Factor

Correct configuration is essential because several of these parameters directly influence communication safety.

---

## 3.9 Compliance Requirements

The protocol shall comply with the following standards:

| Standard           | Purpose                      |
| ------------------ | ---------------------------- |
| DIN VDE V 0831-200 | RaSTA Protocol Specification |
| DIN EN 50159       | Safety-related communication |
| DIN EN 50126       | Railway RAMS process         |
| DIN EN 50128       | Railway software             |
| DIN EN 50129       | Railway signalling safety    |
| IEC 61508          | Functional safety            |

Compliance with these standards ensures that RaSTA can be integrated into safety-related railway signalling systems.

---

## 3.10 Section Summary

The requirements presented in this section define the functional and safety objectives of the RaSTA protocol.

They establish the communication services, timing constraints, compatibility rules, redundancy mechanisms, and safety properties that must be satisfied by every compliant implementation.

These requirements provide the foundation for the protocol architecture and operational behaviour examined in the following sections of this report.
