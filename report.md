# Functional Safety Analysis of the Rail Safe Transport Application (RaSTA) Protocol

## Functional Safety: Designing Systems You Can Trust

---

## Submitted by

**Md Moniruzzaman**

Matriculation Number- 2613370

Bachelor of Science in Information Engineering

HAW Hamburg – Hamburg University of Applied Sciences

---

## Course

**Functional Safety: Designing Systems You Can Trust**

---

## Instructors

**Lars Lange**

**Prof. Pawel Buczek**

---

## Submission Date

**3 July 2026**

---

# Table of Contents

> *Automatically generated.*

---

# List of Figures

| Figure      | Description                                         |
| ----------- | --------------------------------------------------- |
| Figure 2-1  | Classification and Scope of the RaSTA Specification |
| Figure 2-2  | RaSTA Protocol Layers and Message Structure         |
| Figure 2-3  | Interfaces within the RaSTA Protocol Stack          |
| Figure 4-1  | Adaptive Channel Monitoring                         |
| Figure 5-1  | Layered Architecture of the RaSTA Protocol          |
| Figure 5-2  | Interfaces within the RaSTA Protocol Stack          |
| Figure 5-3  | Successful Connection Establishment                 |
| Figure 5-4  | Safety and Retransmission Layer State Machine       |
| Figure 5-5  | Channel Model of the Redundancy Layer               |
| Figure 5-6  | Event-State Matrix of the Redundancy Layer          |
| Figure 5-7  | State Transitions of the Redundancy Layer           |
| Figure 5-8  | Function `f_init()`                                 |
| Figure 5-9  | Function `f_cleanup()`                              |
| Figure 5-10 | Function `f_receiveData()`                          |
| Figure 5-11 | Function `f_sendData()`                             |
| Figure 5-12 | Function `f_deferTmo()`                             |
| Figure 5-13 | Function `f_deliverDeferQueue()`                    |
| Figure 6-1  | Successful Connection Establishment                 |
| Figure 6-2  | Delayed Connection Response                         |
| Figure 6-3  | Delayed Heartbeat During Connection Establishment   |
| Figure 6-4  | Client-Detected Protocol Error                      |
| Figure 6-5  | Normal Application Data Exchange                    |
| Figure 6-6  | Successful Retransmission                           |
| Figure 6-7  | Failed Retransmission                               |

---

# List of Tables

| Table     | Description                                                        |
| --------- | ------------------------------------------------------------------ |
| Table 2-1 | Responsibilities of the RaSTA Protocol Layers                      |
| Table 3-1 | Communication Threats Defined by DIN EN 50159                      |
| Table 3-2 | Mapping of Functional Requirements to Protocol Mechanisms          |
| Table 4-1 | Mission-Critical Functions of RaSTA                                |
| Table 4-2 | Communication Threats and Safety Mechanisms                        |
| Table 5-1 | Responsibilities of the Protocol Layers                            |
| Table 5-2 | General Structure of the Protocol Data Unit                        |
| Table 5-3 | Principal Protocol Message Types                                   |
| Table 5-4 | Contents of the Connection Request                                 |
| Table 5-5 | Contents of the Connection Response                                |
| Table 5-6 | Communication States of the Safety and Retransmission Layer        |
| Table 5-7 | Confirmed Sequence Number Variables                                |
| Table 5-8 | Retransmission States                                              |
| Table 5-9 | Operational States of the Redundancy Layer                         |
| Table 6-1 | Functional Verification During Successful Connection Establishment |
| Table 6-2 | Relationship Between DIN EN 50159 Threats and RaSTA Mechanisms     |
| Table 7-1 | Principal Communication Hazards                                    |
| Table 7-2 | Hazard Mitigation Mechanisms                                       |
| Table 7-3 | Evidence Supporting the Safety Case                                |

---

# List of Abbreviations

| Abbreviation | Meaning                                               |
| ------------ | ----------------------------------------------------- |
| CS           | Confirmed Sequence Number                             |
| CTS          | Confirmed Timestamp                                   |
| DIN          | Deutsches Institut für Normung                        |
| EN           | European Standard                                     |
| HB           | Heartbeat                                             |
| IEC          | International Electrotechnical Commission             |
| MD4          | Message Digest Algorithm 4                            |
| PDU          | Protocol Data Unit                                    |
| RAMS         | Reliability, Availability, Maintainability and Safety |
| RaSTA        | Rail Safe Transport Application                       |
| RFC          | Request for Comments                                  |
| SN           | Sequence Number                                       |
| TCP          | Transmission Control Protocol                         |
| TS           | Timestamp                                             |
| UDP          | User Datagram Protocol                                |

---

# Report Structure

1. Executive Summary
2. System Overview
3. System Requirements
4. Mission Critical Functionality
5. System Architecture
6. Proof of Correct Functional Operation
7. Safety Case
8. Summary
9. References
# 1. Executive Summary

Railway signalling systems are among the most safety-critical infrastructures in modern transportation. Communication failures between signalling components can result in incorrect train movements, reduced operational availability, or hazardous situations that may compromise passenger and operational safety. As railway networks continue to evolve toward distributed and IP-based architectures, communication protocols must provide not only reliable data transfer but also mechanisms that guarantee the integrity, authenticity, sequence, and timeliness of safety-related information.

The **Rail Safe Transport Application (RaSTA)** protocol was developed specifically to address these requirements. Standardized in **DIN VDE V 0831-200**, RaSTA provides a communication framework that complies with the principles of **DIN EN 50159**, enabling safe communication over both closed and open transmission systems. Unlike conventional networking protocols that rely on the reliability of the underlying transport infrastructure, RaSTA assumes that communication channels may experience message corruption, delays, duplication, loss, or incorrect ordering. To mitigate these risks, the protocol incorporates multiple independent safety mechanisms that continuously monitor communication quality throughout the lifetime of every connection.

This report presents a functional safety analysis of the RaSTA communication protocol. The objective is to evaluate how the protocol achieves safe, deterministic, and highly available communication suitable for railway signalling applications. Rather than reproducing the contents of the standard, the report analyses the engineering principles behind the protocol and explains how its design satisfies the communication safety objectives defined by DIN EN 50159.

The analysis begins with an overview of the RaSTA protocol architecture and its position within the railway communication stack. The protocol layers, interfaces, and communication model are introduced to establish the foundation for the subsequent discussion. The report then examines the functional requirements that drive the protocol design, including protection against communication threats such as message corruption, replay, duplication, excessive delay, and communication interruption.

A detailed investigation is subsequently performed on the mission-critical mechanisms implemented by RaSTA. These include adaptive channel monitoring, message integrity verification, sequence number supervision, heartbeat monitoring, retransmission procedures, flow control, redundancy support, and safe connection management. Each mechanism is analysed with respect to its technical operation, engineering purpose, and contribution to overall functional safety.

The report further examines the internal architecture of the protocol, including its protocol data units, communication state machines, connection establishment procedures, and redundancy layer. Official figures from the DIN VDE V 0831-200 specification are used throughout the report to illustrate protocol behaviour and to support the technical discussion. The operational scenarios defined by the standard—including successful communication, connection establishment, retransmission, and failure recovery—are analysed to demonstrate how the protocol maintains deterministic behaviour under both normal and fault conditions.

Finally, the report develops a structured safety case by relating the implemented protocol mechanisms to the communication threats identified in DIN EN 50159. This analysis demonstrates how multiple independent safety mechanisms work together to detect communication faults before they can influence railway signalling applications. The resulting layered defence strategy enables RaSTA to achieve both high communication availability and the fail-safe behaviour required for modern railway systems.

The analysis concludes that RaSTA provides a comprehensive and well-structured solution for safety-related railway communication. Through its combination of integrity protection, sequence supervision, adaptive timing, retransmission, redundancy, and deterministic state management, the protocol effectively addresses the communication hazards associated with safety-critical railway applications. These characteristics have established RaSTA as one of the key communication protocols supporting contemporary digital railway signalling and control systems.

---

### Report Structure

The remainder of this report is organised as follows:

- **Section 2** introduces the RaSTA protocol architecture, communication model, and protocol stack.
- **Section 3** discusses the system requirements and communication safety objectives derived from DIN EN 50159.
- **Section 4** analyses the mission-critical functions that ensure safe communication.
- **Section 5** examines the protocol architecture, message formats, state machines, and protocol operation.
- **Section 6** evaluates the dynamic behaviour of the protocol through the operational scenarios defined in the standard.
- **Section 7** develops the functional safety case by mapping RaSTA mechanisms to the communication threats identified in DIN EN 50159.
- **Section 8** summarises the findings and presents the overall conclusions of the analysis.
# 1. Executive Summary

Railway signalling systems are among the most safety-critical infrastructures in modern transportation. Communication failures between signalling components can result in incorrect train movements, reduced operational availability, or hazardous situations that may compromise passenger and operational safety. As railway networks continue to evolve toward distributed and IP-based architectures, communication protocols must provide not only reliable data transfer but also mechanisms that guarantee the integrity, authenticity, sequence, and timeliness of safety-related information.

The **Rail Safe Transport Application (RaSTA)** protocol was developed specifically to address these requirements. Standardized in **DIN VDE V 0831-200**, RaSTA provides a communication framework that complies with the principles of **DIN EN 50159**, enabling safe communication over both closed and open transmission systems. Unlike conventional networking protocols that rely on the reliability of the underlying transport infrastructure, RaSTA assumes that communication channels may experience message corruption, delays, duplication, loss, or incorrect ordering. To mitigate these risks, the protocol incorporates multiple independent safety mechanisms that continuously monitor communication quality throughout the lifetime of every connection.

This report presents a functional safety analysis of the RaSTA communication protocol. The objective is to evaluate how the protocol achieves safe, deterministic, and highly available communication suitable for railway signalling applications. Rather than reproducing the contents of the standard, the report analyses the engineering principles behind the protocol and explains how its design satisfies the communication safety objectives defined by DIN EN 50159.

The analysis begins with an overview of the RaSTA protocol architecture and its position within the railway communication stack. The protocol layers, interfaces, and communication model are introduced to establish the foundation for the subsequent discussion. The report then examines the functional requirements that drive the protocol design, including protection against communication threats such as message corruption, replay, duplication, excessive delay, and communication interruption.

A detailed investigation is subsequently performed on the mission-critical mechanisms implemented by RaSTA. These include adaptive channel monitoring, message integrity verification, sequence number supervision, heartbeat monitoring, retransmission procedures, flow control, redundancy support, and safe connection management. Each mechanism is analysed with respect to its technical operation, engineering purpose, and contribution to overall functional safety.

The report further examines the internal architecture of the protocol, including its protocol data units, communication state machines, connection establishment procedures, and redundancy layer. Official figures from the DIN VDE V 0831-200 specification are used throughout the report to illustrate protocol behaviour and to support the technical discussion. The operational scenarios defined by the standard—including successful communication, connection establishment, retransmission, and failure recovery—are analysed to demonstrate how the protocol maintains deterministic behaviour under both normal and fault conditions.

Finally, the report develops a structured safety case by relating the implemented protocol mechanisms to the communication threats identified in DIN EN 50159. This analysis demonstrates how multiple independent safety mechanisms work together to detect communication faults before they can influence railway signalling applications. The resulting layered defence strategy enables RaSTA to achieve both high communication availability and the fail-safe behaviour required for modern railway systems.

The analysis concludes that RaSTA provides a comprehensive and well-structured solution for safety-related railway communication. Through its combination of integrity protection, sequence supervision, adaptive timing, retransmission, redundancy, and deterministic state management, the protocol effectively addresses the communication hazards associated with safety-critical railway applications. These characteristics have established RaSTA as one of the key communication protocols supporting contemporary digital railway signalling and control systems.

---

### Report Structure

The remainder of this report is organised as follows:

- **Section 2** introduces the RaSTA protocol architecture, communication model, and protocol stack.
- **Section 3** discusses the system requirements and communication safety objectives derived from DIN EN 50159.
- **Section 4** analyses the mission-critical functions that ensure safe communication.
- **Section 5** examines the protocol architecture, message formats, state machines, and protocol operation.
- **Section 6** evaluates the dynamic behaviour of the protocol through the operational scenarios defined in the standard.
- **Section 7** develops the functional safety case by mapping RaSTA mechanisms to the communication threats identified in DIN EN 50159.
- **Section 8** summarises the findings and presents the overall conclusions of the analysis.
# 2. System Overview

## 2.1 Introduction

The Rail Safe Transport Application (RaSTA) protocol is a standardized communication protocol developed for safety-related railway signalling and control systems. It provides secure, deterministic, and highly available communication between distributed railway applications while remaining independent of the underlying communication technology.

Unlike conventional communication protocols, RaSTA assumes that the transmission network cannot always be trusted. Messages may be delayed, corrupted, duplicated, reordered, or even lost during transmission. Consequently, the protocol performs its own communication supervision instead of relying solely on the transport layer.

The protocol is specified in **DIN VDE V 0831-200** and is designed to satisfy the communication safety principles defined in **DIN EN 50159**. Through a combination of integrity protection, adaptive timing supervision, sequence monitoring, retransmission, and redundancy support, RaSTA provides a communication service suitable for applications requiring high levels of functional safety.

This chapter introduces the overall architecture of the protocol, explains its position within the communication stack, and presents the layered design philosophy upon which the remainder of the report is based.

---

# 2.2 Position of RaSTA within the Communication Architecture

RaSTA is positioned between the application software and the underlying transport network. It provides safety-related communication services without requiring modifications to existing communication infrastructures such as Ethernet or IP networks.

![Classification and Delimitation of the RaSTA Specification](figures/figure-2-1-rasta-specification-scope.png)

**Figure 2-1.** Classification and delimitation of the RaSTA specification (adapted from DIN VDE V 0831-200 [1]).

Figure 2-1 illustrates the scope of the RaSTA specification. The protocol standard primarily defines two protocol layers:

- **Safety and Retransmission Layer**
- **Redundancy Layer**

These layers together provide the safety-related communication functions required for railway applications. The application layer above RaSTA and the transport technologies below RaSTA are intentionally left outside the scope of the standard.

This separation allows RaSTA to remain largely independent of both the application software and the physical communication infrastructure. Consequently, different railway applications can reuse the same protocol implementation, while different transport technologies can be employed without changing the protocol behaviour.

The standard therefore focuses on defining communication safety mechanisms rather than specifying a complete networking solution.

---

# 2.3 Layered Protocol Architecture

The layered architecture is one of the key design principles of the RaSTA protocol.

![RaSTA Protocol Layers](figures/figure-2-2-rasta-protocol-layers.png)

**Figure 2-2.** RaSTA protocol layers and message structure (adapted from DIN VDE V 0831-200 [1]).

As shown in Figure 2-2, communication is divided into several independent layers.

At the top of the architecture, the application layer generates safety-related information such as signalling commands, status information, or diagnostic data. The application itself is not responsible for communication safety.

Instead, every outgoing message is first processed by the **Safety and Retransmission Layer**. This layer adds protocol control information including sequence numbers, timestamps, sender and receiver identifiers, and message integrity protection. It also supervises communication timing and performs retransmissions whenever communication errors occur.

The processed message is then passed to the **Redundancy Layer**, whose responsibility is to increase communication availability. When multiple transport channels are available, identical protocol messages are transmitted simultaneously over each channel. At the receiving side, the redundancy layer reconstructs a single logical communication channel from the available physical channels.

Finally, the transport adaptation forwards the completed protocol message using existing communication technologies such as UDP or TCP over IP networks.

This modular architecture separates communication safety from data transmission, allowing each protocol layer to perform a dedicated engineering function while minimizing dependencies between layers.

---

# 2.4 Separation of Responsibilities

A notable feature of the RaSTA architecture is that each protocol layer is responsible for a specific set of communication tasks.

| Protocol Layer | Primary Responsibility |
|---------------|------------------------|
| Application Layer | Generates and consumes application data. |
| Safety and Retransmission Layer | Communication safety, integrity verification, sequencing, timing supervision, retransmission, connection management. |
| Redundancy Layer | Communication availability through multiple transport channels. |
| Transport Adaptation | Mapping protocol messages to the underlying transport service. |
| Transport Layer | Physical transmission of protocol messages. |

**Table 2-1. Responsibilities of the protocol layers.**

This strict separation simplifies implementation and verification. Each layer can evolve independently as long as the interfaces between layers remain unchanged.

Furthermore, faults occurring in one layer remain isolated and cannot directly modify the behaviour of the remaining protocol layers.

---

# 2.5 Protocol Interfaces

Communication between the protocol layers is achieved through standardized interfaces.

![Protocol Stack Interfaces](figures/figure-2-3-protocol-stack-interfaces.png)

**Figure 2-3.** Interfaces within the RaSTA protocol stack (adapted from DIN VDE V 0831-200 [1]).

The protocol specification distinguishes between **compatibility-relevant** and **non-compatibility-relevant** interfaces.

Compatibility-relevant interfaces define the externally observable behaviour of the protocol and therefore must be implemented exactly as specified by the standard. These include protocol message formats, protocol state transitions, communication procedures, and message validation rules. Compliance with these requirements ensures interoperability between independent RaSTA implementations.

Non-compatibility-relevant interfaces describe interactions between the protocol and its surrounding software environment, such as the application programming interface or the transport adaptation layer. These interfaces may differ between implementations provided that the externally visible protocol behaviour remains unchanged.

This distinction provides implementation flexibility while preserving protocol interoperability across different railway systems.

---

# 2.6 Communication Model

RaSTA assumes that the communication network itself does not guarantee safe transmission. Instead, communication safety is achieved entirely through protocol mechanisms implemented within the Safety and Retransmission Layer.

Consequently, the protocol treats the transport network as a best-effort communication service capable of introducing faults such as:

- Message corruption
- Message duplication
- Message loss
- Message reordering
- Excessive communication delay
- Temporary communication interruption

Rather than attempting to prevent these faults, RaSTA continuously monitors communication and detects their occurrence before the received information reaches the application layer.

This philosophy differs significantly from traditional communication protocols, where reliable network infrastructure is often assumed. In RaSTA, safety is achieved through protocol supervision rather than network reliability.

---

# 2.7 Overview of Protocol Operation

From a high-level perspective, communication proceeds through the following sequence:

1. The application requests the establishment of a communication connection.
2. The Safety and Retransmission Layer performs a protocol handshake.
3. Once synchronization has been completed, application data is transmitted.
4. Every transmitted message is continuously supervised using integrity verification, sequence monitoring, adaptive timing, and heartbeat messages.
5. Whenever communication errors occur, retransmission or redundancy mechanisms attempt to restore correct communication.
6. If safe communication can no longer be guaranteed, the protocol terminates the connection and enters a predefined safe state.

This operational philosophy ensures that communication remains deterministic throughout the complete lifetime of every connection.

---

# 2.8 Section Summary

This chapter introduced the overall architecture of the RaSTA protocol and its position within the railway communication stack. The layered design clearly separates application functionality, communication safety, redundancy management, and transport services, allowing each layer to perform a dedicated role while maintaining protocol modularity.

The official protocol architecture presented in DIN VDE V 0831-200 demonstrates that communication safety is implemented independently of the underlying transmission technology. Rather than relying on the communication network itself, RaSTA achieves functional safety through protocol-level supervision mechanisms implemented within the Safety and Retransmission Layer.

The following chapter examines the system requirements that motivated the development of these mechanisms and analyses the communication safety objectives derived from DIN EN 50159.
# 3. System Requirements

## 3.1 Introduction

Safety-related railway communication differs fundamentally from conventional data communication. While common networking applications such as web browsing or multimedia streaming can tolerate occasional packet loss, delays, or retransmissions with little consequence, signalling systems responsible for controlling train movement require communication that is both reliable and demonstrably safe.

Incorrect or outdated information exchanged between signalling components may result in hazardous operating conditions. Consequently, communication protocols used within railway control systems must satisfy strict functional safety requirements in addition to traditional communication performance requirements.

The Rail Safe Transport Application (RaSTA) protocol was developed specifically to satisfy these requirements. Its design is based on the communication safety principles defined by **DIN EN 50159**, which specifies how safety-related communication shall be protected when transmitted over communication networks that cannot be assumed to be completely trustworthy.

This chapter introduces the communication threats identified by DIN EN 50159 and explains the corresponding system requirements that motivated the design of the RaSTA protocol.

---

# 3.2 Functional Safety Requirements

The primary objective of functional safety is to ensure that a system performs its intended safety functions correctly under both normal operating conditions and reasonably foreseeable fault conditions.

Within railway signalling systems, this objective extends beyond the signalling equipment itself and includes the communication links connecting distributed safety functions.

Communication therefore becomes part of the overall safety function.

For a communication protocol to be suitable for railway signalling applications, it must guarantee that transmitted information satisfies several fundamental properties:

- Correctness
- Integrity
- Availability
- Timeliness
- Authenticity
- Deterministic behaviour

Failure to satisfy any one of these properties may prevent the signalling system from performing its intended safety function.

Rather than assuming that communication failures are unlikely, railway communication protocols must actively detect such failures before unsafe information reaches the application layer.

This philosophy forms the basis of the RaSTA protocol.

---

# 3.3 Communication Threats Defined by DIN EN 50159

DIN EN 50159 identifies several communication faults that may occur when safety-related information is transmitted through communication networks.

Unlike conventional protocols, RaSTA assumes that each of these faults may occur at any time and therefore continuously supervises communication throughout the lifetime of every connection.

The principal communication threats are summarised in Table 3-1.

| Communication Threat | Description |
|----------------------|-------------|
| Message Corruption | One or more bits of a transmitted message are modified during transmission. |
| Message Loss | A transmitted message never reaches the receiver. |
| Message Duplication | The same message is delivered multiple times. |
| Message Insertion | An unexpected or unauthorised message appears within the communication channel. |
| Message Resequencing | Messages arrive in an order different from that in which they were transmitted. |
| Excessive Delay | Messages arrive after an unacceptable period of time. |
| Communication Interruption | Communication between two systems stops unexpectedly. |
| Replay | Previously transmitted messages are delivered again at a later time. |

**Table 3-1. Principal communication threats identified by DIN EN 50159.**

Each of these threats may compromise functional safety if not detected before the received information reaches the railway application.

Consequently, RaSTA implements dedicated protocol mechanisms that continuously supervise communication and detect these faults.

---

# 3.4 Safety Objectives of RaSTA

To address the communication threats defined by DIN EN 50159, the RaSTA protocol establishes several communication safety objectives.

The protocol must ensure that:

- every received message originates from the expected communication partner;
- every received message remains identical to the transmitted message;
- every message is received in the correct order;
- missing messages are detected;
- duplicated messages are rejected;
- delayed messages are identified;
- communication interruptions are detected within a predefined time;
- communication always terminates safely whenever correctness can no longer be guaranteed.

Unlike many communication protocols that focus primarily on performance, RaSTA gives priority to communication correctness.

Whenever uncertainty exists regarding the validity of received information, the protocol intentionally terminates communication rather than risking unsafe operation.

This fail-safe behaviour represents one of the most important functional safety characteristics of the protocol.

---

# 3.5 Communication Performance Requirements

In addition to communication safety, railway signalling systems also require high communication availability.

Unexpected communication interruptions may reduce railway capacity, increase operational delays, or prevent signalling equipment from exchanging necessary operational data.

Consequently, RaSTA has been designed to provide both safety and availability simultaneously.

The protocol therefore incorporates several mechanisms that improve communication performance without compromising safety.

These include:

- adaptive channel monitoring;
- retransmission of lost messages;
- heartbeat supervision;
- redundant communication channels;
- flow control;
- packetization of application messages.

Rather than simply disconnecting after every communication disturbance, the protocol first attempts to restore communication whenever this can be achieved safely.

Only when recovery is no longer possible does the protocol terminate the connection.

This approach provides high operational availability while maintaining deterministic safety behaviour.

---

# 3.6 Design Requirements

The architecture of RaSTA was developed according to several fundamental engineering principles.

First, communication safety shall be independent of the underlying communication technology.

Whether communication occurs through Ethernet, fibre optic networks, wireless links, or IP-based infrastructures, the protocol shall provide identical safety behaviour.

Second, communication shall remain deterministic.

For every protocol event, the protocol defines exactly one permitted response.

This deterministic behaviour greatly simplifies protocol verification, validation, and safety certification.

Third, communication shall remain modular.

The application software, communication protocol, redundancy management, and transport services are separated into independent layers.

This modular architecture allows each layer to evolve independently without affecting the externally observable behaviour of the protocol.

Finally, communication shall remain interoperable.

Different implementations developed by independent manufacturers must communicate correctly provided that they implement the compatibility-relevant requirements defined by DIN VDE V 0831-200.

---

# 3.7 Relationship Between Requirements and Protocol Mechanisms

The communication requirements discussed throughout this chapter directly influence the architecture of the RaSTA protocol.

Table 3-2 summarises this relationship.

| System Requirement | Primary RaSTA Mechanism |
|--------------------|-------------------------|
| Message Integrity | MD4 Security Code |
| Message Authenticity | Sender and Receiver Identification |
| Correct Message Order | Sequence Number Supervision |
| Message Recovery | Retransmission Procedure |
| Timeliness | Adaptive Channel Monitoring |
| Connection Supervision | Heartbeat Messages |
| High Availability | Redundancy Layer |
| Buffer Protection | Flow Control |
| Safe Failure | Disconnect Procedure |

**Table 3-2. Relationship between system requirements and RaSTA protocol mechanisms.**

Rather than relying on a single communication safeguard, RaSTA combines multiple independent mechanisms to satisfy the communication safety objectives required by DIN EN 50159.

This layered design significantly increases communication robustness and forms the foundation for the protocol architecture analysed in the following chapters.

---

# 3.8 Section Summary

This chapter introduced the functional safety requirements that motivated the development of the RaSTA protocol. The communication threats identified by DIN EN 50159 demonstrate that communication networks cannot be assumed to behave perfectly and therefore require continuous supervision when used for safety-related railway applications.

To address these threats, RaSTA establishes clear communication objectives including integrity, authenticity, timeliness, availability, determinism, and fail-safe behaviour. These requirements directly influence the design of the protocol mechanisms that will be analysed in the following chapters.

The next chapter examines these mission-critical mechanisms in detail and explains how they collectively provide safe communication suitable for modern railway signalling systems.
# 4. Mission Critical Functionality

## 4.1 Introduction

The primary objective of the Rail Safe Transport Application (RaSTA) protocol is to ensure that safety-related information exchanged between railway signalling systems remains correct, complete, timely, and available throughout the entire communication process. Unlike conventional communication protocols that depend largely on the reliability of the underlying network infrastructure, RaSTA assumes that communication channels may introduce errors at any time. Consequently, the protocol continuously supervises communication and verifies every received message before it is delivered to the application.

The design of RaSTA follows the communication safety principles defined in **DIN EN 50159**, which identifies communication threats such as message corruption, duplication, loss, replay, resequencing, excessive delay, and communication interruption. Instead of relying on a single protection mechanism, RaSTA combines multiple independent safety functions that operate simultaneously throughout the lifetime of every communication connection.

The mission-critical mechanisms implemented by the protocol ensure that communication failures are detected before they can influence the behaviour of railway signalling applications. Whenever communication correctness can no longer be guaranteed, the protocol intentionally terminates the connection and transitions into a predefined safe state.

This chapter introduces the principal safety mechanisms implemented by RaSTA and explains their contribution to functional safety. Detailed protocol behaviour and message processing are discussed later in Sections 5 and 6.

---

# 4.2 Safety Mechanisms within RaSTA

The communication safety provided by RaSTA is achieved through several independent protocol mechanisms that continuously supervise different aspects of communication.

The principal mission-critical functions are summarised in Table 4-1.

| Safety Mechanism | Purpose |
|------------------|---------|
| Connection Management | Establishes and terminates safe communication sessions. |
| Message Integrity Verification | Detects message corruption during transmission. |
| Sequence Supervision | Detects lost, duplicated and replayed messages. |
| Adaptive Time Monitoring | Detects excessive communication delays. |
| Heartbeat Monitoring | Supervises communication during idle periods. |
| Retransmission | Recovers lost application messages. |
| Flow Control | Prevents receiver buffer overflow. |
| Redundancy | Improves communication availability. |
| Safe Disconnection | Terminates communication whenever correctness cannot be guaranteed. |

**Table 4-1. Principal mission-critical functions implemented by RaSTA.**

Rather than acting independently, these mechanisms operate together to create multiple layers of protection against communication faults. This layered architecture significantly increases the robustness of safety-related communication.

---

# 4.3 Communication Interfaces

The protocol separates the application software from the communication mechanisms through clearly defined interfaces.

![Protocol Interfaces](figures/figure-2-3-protocol-stack-interfaces.png)

**Figure 4-1. Interfaces within the RaSTA protocol stack (adapted from DIN VDE V 0831-200 [1]).**

The application communicates with RaSTA through services such as:

- OpenConnection()
- CloseConnection()
- SendData()
- ReceiveData()
- ConnectionStateRequest()

Similarly, the transport layer exchanges protocol data units with the redundancy layer without requiring knowledge of the protocol internals.

This separation allows signalling applications to remain independent of communication technology while enabling RaSTA to provide a standardised communication service across different platforms.

---

# 4.4 Adaptive Channel Monitoring

One of the most important innovations of RaSTA is its adaptive approach to communication supervision.

![Adaptive Channel Monitoring](figures/figure-4-1-adaptive-channel-monitoring.png)

**Figure 4-1.** Adaptive channel monitoring (adapted from DIN VDE V 0831-200 [1]).

Instead of relying on fixed timeout values, the protocol continuously measures communication behaviour by monitoring round-trip delays and heartbeat timing. These measurements are used to calculate dynamic supervision timers that reflect current network conditions.

This adaptive approach provides two important advantages.

First, temporary increases in communication latency do not unnecessarily interrupt communication.

Second, genuine communication failures are detected rapidly, allowing the protocol to terminate unsafe communication before obsolete information reaches the signalling application.

Adaptive timing therefore contributes directly to the communication safety objectives defined by DIN EN 50159.

---

# 4.5 Message Integrity and Sequence Supervision

Every protocol data unit generated by RaSTA contains additional control information that enables continuous supervision of communication correctness.

The protocol verifies:

- sender identity;
- receiver identity;
- message integrity;
- sequence numbers;
- confirmed sequence numbers;
- timestamps.

Integrity verification is performed using an MD4-based safety code calculated over the protocol message before transmission. Upon reception, the receiver performs the same calculation and compares the resulting value with the transmitted safety code. Any discrepancy indicates message corruption, and the message is discarded immediately.

Sequence numbers provide a second layer of protection. Each transmitted message receives a unique sequence number, allowing the receiver to detect missing, duplicated, replayed, or incorrectly ordered messages.

Together, integrity verification and sequence supervision ensure that only correct and complete communication reaches the application layer.

---

# 4.6 Heartbeat and Connection Supervision

Communication safety must be maintained even when no application data is being exchanged.

For this reason, RaSTA periodically transmits **Heartbeat** messages whenever the configured heartbeat interval (**Th**) expires without application traffic.

Heartbeat messages confirm that both communication partners remain operational while simultaneously updating the adaptive timing calculations.

If the receiver does not receive a valid heartbeat or application message within the calculated supervision interval, the communication partner is considered unavailable and the connection is terminated safely.

Continuous heartbeat supervision therefore allows communication interruptions to be detected even during idle periods.

---

# 4.7 Retransmission and Flow Control

Temporary communication disturbances do not necessarily require the communication session to be terminated.

Whenever the receiver detects a missing sequence number, it requests retransmission of the missing information from the sender.

Only application data messages are retransmitted, preserving communication efficiency while maintaining message consistency.

In addition, RaSTA implements flow control to prevent receiver buffer overflow.

During connection establishment, both communication partners exchange their receive buffer capacities. The sender then limits the number of outstanding unacknowledged messages accordingly, ensuring stable communication regardless of differences in processing performance.

Together, retransmission and flow control improve communication availability without compromising safety.

---

# 4.8 Redundancy

For applications requiring particularly high availability, RaSTA supports redundant communication channels.

Instead of transmitting protocol messages over a single transport channel, identical messages may be transmitted simultaneously through multiple independent communication paths.

The redundancy layer reconstructs a single logical communication channel from the received transport channels.

Consequently, failure of one communication path does not necessarily interrupt communication.

This architecture significantly improves communication availability while maintaining the same protocol behaviour observed by the Safety and Retransmission Layer.

---

# 4.9 Contribution to Functional Safety

Each safety mechanism implemented by RaSTA addresses one or more communication threats identified by DIN EN 50159.

| Communication Threat | Primary Protection Mechanism |
|----------------------|------------------------------|
| Message corruption | MD4 safety code |
| Message loss | Retransmission |
| Message duplication | Sequence supervision |
| Replay | Sequence supervision |
| Message resequencing | Sequence supervision |
| Excessive delay | Adaptive channel monitoring |
| Communication interruption | Heartbeat supervision |
| Buffer overflow | Flow control |

**Table 4-2. Relationship between communication threats and RaSTA safety mechanisms.**

The protocol therefore employs multiple independent barriers against communication faults rather than relying on any single protection mechanism.

---

# 4.10 Section Summary

This chapter introduced the mission-critical mechanisms that enable RaSTA to provide safe railway communication. Through message integrity verification, sequence supervision, adaptive timing, heartbeat monitoring, retransmission, flow control, and redundancy, the protocol continuously evaluates communication correctness throughout the lifetime of every connection.

Rather than assuming that the communication network behaves reliably, RaSTA verifies that every message satisfies the required safety conditions before it is accepted by the receiving application. Whenever these conditions are no longer fulfilled, communication is terminated in a deterministic and fail-safe manner.

The following chapter examines the internal architecture of the protocol in greater detail, including protocol data units, state machines, connection establishment procedures, and message processing defined by DIN VDE V 0831-200.
# 5. System Architecture

## 5.1 Introduction

The Rail Safe Transport Application (RaSTA) protocol achieves functional safety through a modular architecture that separates communication safety from communication availability. Instead of implementing all protocol functions within a single software component, RaSTA divides its responsibilities into independent protocol layers, each performing a clearly defined task.

This architectural separation provides several important engineering advantages. It simplifies implementation, improves maintainability, enables independent verification of protocol components, and allows the protocol to operate over different transport technologies without affecting safety-related behaviour.

Unlike conventional communication protocols that rely heavily on reliable transport services, RaSTA assumes that the underlying communication network may introduce message corruption, duplication, delays, or packet loss. Consequently, all safety-related verification is performed inside the protocol itself.

This chapter examines the internal architecture defined by **DIN VDE V 0831-200**, beginning with the layered protocol structure before analysing protocol messages, connection establishment, state machines, retransmission, and redundancy.

---

## 5.2 Layered Architecture

The RaSTA protocol is organised into two principal protocol layers:

- Safety and Retransmission Layer
- Redundancy Layer

Together these layers provide both communication safety and communication availability.

![RaSTA Protocol Layers](figures/figure-2-2-rasta-protocol-layers.png)

**Figure 5-1. Layered architecture of the RaSTA protocol (adapted from DIN VDE V 0831-200 [1]).**

The **Application Layer** generates signalling information such as movement authorities, status information, or diagnostic data. It has no knowledge of the communication mechanisms implemented by RaSTA.

The **Safety and Retransmission Layer** forms the core of the protocol. Every outgoing application message passes through this layer before transmission. It performs message integrity verification, sequence supervision, adaptive timing supervision, heartbeat generation, retransmission, connection management, and flow control.

Below the Safety Layer, the **Redundancy Layer** improves communication availability. If multiple transport channels exist, identical protocol messages are transmitted simultaneously through each channel. At the receiving side, the redundancy layer reconstructs a single logical communication channel before forwarding messages to the Safety Layer.

Finally, the **Transport Layer** carries the completed protocol message using technologies such as UDP or TCP over Ethernet.

This layered organisation ensures that communication safety remains independent of the transport technology while allowing the protocol to operate over different network infrastructures without modification.

---

## 5.3 Responsibilities of the Protocol Layers

Each protocol layer performs a dedicated engineering function.

| Protocol Layer | Primary Responsibility |
|---------------|------------------------|
| Application | Generates and consumes railway signalling information. |
| Safety and Retransmission Layer | Communication safety, message integrity, timing supervision, retransmission, connection management. |
| Redundancy Layer | Communication availability through multiple transport channels. |
| Transport Adaptation | Maps RaSTA messages onto the selected transport protocol. |
| Transport Layer | Physical transmission of protocol data. |

**Table 5-1. Responsibilities of the RaSTA protocol layers.**

Separating these responsibilities simplifies protocol verification because each layer can be analysed independently while preserving the externally observable behaviour defined by the standard.

---

## 5.4 Safety and Retransmission Layer

The Safety and Retransmission Layer implements the majority of the protocol mechanisms responsible for functional safety.

Its principal responsibilities include:

- establishing communication,
- supervising protocol states,
- verifying message integrity,
- monitoring sequence numbers,
- detecting communication timeouts,
- generating heartbeat messages,
- requesting retransmission,
- performing flow control,
- terminating unsafe communication.

Unlike ordinary transport protocols, this layer continuously evaluates communication correctness rather than assuming that the transport network provides reliable delivery.

Every received protocol message is verified before being accepted.

If verification fails, the protocol either initiates recovery or safely terminates the communication session.

This behaviour represents the primary safety barrier between the railway signalling application and the communication network.

---

## 5.5 Protocol Interfaces

Communication between the protocol layers is performed through standardised interfaces defined by DIN VDE V 0831-200.

![Protocol Stack Interfaces](figures/figure-2-3-protocol-stack-interfaces.png)

**Figure 5-2. Interfaces within the RaSTA protocol stack (adapted from DIN VDE V 0831-200 [1]).**

The specification distinguishes between **compatibility-relevant** and **non-compatibility-relevant** interfaces.

Compatibility-relevant interfaces define externally observable protocol behaviour, including message formats, protocol state transitions, sequence number processing, and communication procedures. Every compliant RaSTA implementation must implement these interfaces exactly as specified to ensure interoperability.

Non-compatibility-relevant interfaces connect the protocol to the application software and the transport adaptation layer. These interfaces may vary between implementations provided that the externally visible protocol behaviour remains unchanged.

This separation enables manufacturers to optimise software architecture while maintaining full compatibility with other RaSTA implementations.

---

## 5.6 Protocol Data Unit

Every message exchanged between two RaSTA instances is encapsulated inside a **Protocol Data Unit (PDU)**.

The protocol data unit contains both application information and protocol control information required for communication supervision.

The general structure is summarised in Table 5-2.

| Field | Purpose |
|--------|---------|
| Message Length | Total protocol message size |
| Message Type | Identifies the protocol function |
| Receiver ID | Identifies the destination |
| Sender ID | Identifies the source |
| Sequence Number | Maintains communication order |
| Confirmed Sequence Number | Acknowledges previously received messages |
| Timestamp | Supports adaptive timing supervision |
| Confirmed Timestamp | Confirms previous timing information |
| User Data | Application payload |
| Security Code | Protects message integrity |

**Table 5-2. General structure of the RaSTA Protocol Data Unit (adapted from DIN VDE V 0831-200 [1]).**

Unlike ordinary communication protocols, every protocol message simultaneously performs several communication safety functions.

The sequence numbers supervise communication order.

The timestamps support adaptive timeout calculation.

The sender and receiver identifiers prevent communication with unintended protocol partners.

Finally, the Security Code protects the complete protocol message against accidental modification during transmission.

The protocol therefore combines multiple independent safety mechanisms within every transmitted message without requiring additional supervisory traffic.

---

## 5.7 Protocol Message Types

RaSTA defines a small number of specialised protocol messages that collectively support the complete communication lifecycle.

| Message | Purpose |
|----------|---------|
| Connection Request | Begins communication establishment. |
| Connection Response | Confirms communication parameters. |
| Data | Transfers application information. |
| Heartbeat | Supervises communication during idle periods. |
| Retransmission Request | Requests missing messages. |
| Retransmission Response | Confirms retransmission. |
| Retransmitted Data | Resends previously lost application data. |
| Disconnect Request | Safely terminates communication. |

**Table 5-3. Principal protocol messages defined by RaSTA.**

Although only a limited number of message types exist, they support all protocol operations including connection establishment, communication supervision, error recovery, and safe connection termination.

Every message follows the same protocol data unit structure introduced previously. Consequently, the receiver processes all protocol messages using a common verification procedure before applying message-specific behaviour.

---

## 5.8 Section Summary

This first part introduced the internal architecture of the RaSTA protocol. The layered organisation clearly separates communication safety from communication availability while allowing the protocol to operate independently of the underlying transport technology.

The Safety and Retransmission Layer provides the principal communication safety mechanisms, whereas the Redundancy Layer improves communication availability. Together they form a modular protocol architecture capable of supporting safety-related railway communication.

The following section examines how these protocol messages are used during connection establishment, including the three-stage handshake, protocol version negotiation, and synchronization of communication partners before application data exchange begins.

## 5.9 Connection Establishment

Before any application data can be exchanged, the communication partners must establish a synchronized communication session. This initialization procedure ensures that both sides share identical protocol parameters and communication context before entering normal operation.

Unlike conventional networking protocols, where communication may begin immediately after a simple connection request, RaSTA performs a structured handshake that verifies protocol compatibility, synchronizes sequence numbers, exchanges communication parameters, and establishes timing supervision.

Only after all verification steps have been completed successfully does the protocol permit safety-related application data to be transmitted.

The complete communication procedure is illustrated in Figure 5-3.

![Successful Connection Establishment](figures/figure-6-1-successful-connection-establishment.png)

**Figure 5-3. Successful connection establishment according to DIN VDE V 0831-200 [1].**

The communication sequence consists of three protocol messages:

1. Connection Request (ConnReq)
2. Connection Response (ConnResp)
3. Heartbeat (HB)

Each message contributes to the initialization of the communication context and provides evidence that both communication partners have reached the same protocol state.

---

## 5.10 Connection Request

Communication begins when the client transmits a **Connection Request (ConnReq)**.

This message contains all information required for the receiver to establish a new protocol context.

The principal information exchanged is summarised in Table 5-4.

| Field | Purpose |
|--------|---------|
| Receiver ID | Identifies the destination RaSTA instance |
| Sender ID | Identifies the transmitting RaSTA instance |
| Protocol Version | Indicates the supported protocol version |
| Initial Sequence Number | Defines the starting point for sequence supervision |
| Timestamp | Provides the initial timing reference |
| Receive Buffer Size (Nsendmax) | Advertises receiver capacity |
| Security Code | Protects the complete protocol message |

**Table 5-4. Principal contents of the Connection Request message (adapted from DIN VDE V 0831-200 [1]).**

One important design decision is the use of a **random initial sequence number**.

Rather than beginning every communication session with sequence number zero, RaSTA selects a random starting value for each new connection.

This approach significantly reduces the possibility of replay attacks because messages captured during previous communication sessions cannot accidentally satisfy the sequence verification rules of a newly established connection.

The Connection Request therefore performs much more than requesting communication. It establishes the initial communication context upon which all subsequent safety mechanisms depend.

---

## 5.11 Connection Response

After successfully validating the received Connection Request, the receiver generates a **Connection Response (ConnResp)**.

The Connection Response acknowledges the received request while simultaneously transmitting the receiver's own communication parameters.

The information exchanged is summarised in Table 5-5.

| Field | Purpose |
|--------|---------|
| Receiver ID | Destination communication partner |
| Sender ID | Responding RaSTA instance |
| Protocol Version | Accepted protocol version |
| Initial Sequence Number | Receiver's random sequence number |
| Confirmed Sequence Number | Acknowledges the Connection Request |
| Timestamp | Current communication time |
| Confirmed Timestamp | Confirms the received timestamp |
| Receive Buffer Size | Receiver capacity |
| Security Code | Protects message integrity |

**Table 5-5. Principal contents of the Connection Response message (adapted from DIN VDE V 0831-200 [1]).**

During this stage, both communication partners exchange the parameters required for subsequent communication. In addition to sequence numbers and timing information, the exchange of receive buffer sizes allows the flow control mechanism to operate correctly during normal communication.

Every field contained within the Connection Response is verified before communication proceeds.

---

## 5.12 Completion of the Handshake

The final stage of the initialization procedure is the transmission of a **Heartbeat (HB)** message by the client.

Although Heartbeat messages are primarily used during normal operation to supervise communication, the first Heartbeat serves a special purpose during connection establishment.

Its successful reception confirms that:

- both communication partners have synchronized sequence numbers;
- protocol versions are compatible;
- timing information has been exchanged successfully;
- communication parameters have been accepted by both sides.

Only after this Heartbeat has been received do both protocol instances transition into the **Up** state.

At this point the communication channel is considered operational, and application data may be exchanged safely.

This additional confirmation step distinguishes RaSTA from many conventional communication protocols and significantly increases confidence that both communication partners possess an identical communication context before normal operation begins.

---

## 5.13 Protocol Version Negotiation

Compatibility between protocol implementations is verified during connection establishment.

Every Connection Request contains the protocol version supported by the initiating communication partner.

The receiver compares this version with its own implementation before accepting the connection.

Three situations are possible:

- Both communication partners support the same protocol version.
- One communication partner supports a newer but compatible version.
- The protocol versions are incompatible.

If compatibility cannot be established, communication is terminated immediately.

The protocol never attempts to exchange safety-related information using incompatible implementations because differences in protocol interpretation could lead to unpredictable behaviour.

Version negotiation therefore represents an additional layer of protection that preserves interoperability while maintaining deterministic communication behaviour.

---

## 5.14 Functional Safety Contribution

The connection establishment procedure provides the foundation for all subsequent protocol operation.

Rather than simply opening a communication channel, the handshake establishes a synchronized protocol context shared by both communication partners.

From a functional safety perspective, this procedure provides several important benefits:

- Prevents communication with incompatible protocol implementations.
- Synchronizes sequence numbers before application data exchange.
- Establishes adaptive timing supervision.
- Exchanges communication buffer capacities.
- Prevents replay attacks through random sequence number initialization.
- Confirms that both protocol instances have reached identical communication states.

Only after every verification step has completed successfully does the protocol permit application messages to influence the railway signalling system.

Consequently, the connection establishment procedure represents the first safety barrier implemented by the RaSTA protocol before normal communication begins.

---

## 5.15 Section Summary

This section analysed the connection establishment procedure defined by DIN VDE V 0831-200. Through the exchange of the Connection Request, Connection Response, and Heartbeat messages, both communication partners establish a synchronized communication context before application data transmission begins.

The handshake performs considerably more than simply opening a communication session. It synchronizes protocol versions, sequence numbers, timing information, communication buffers, and sender identification while simultaneously protecting against replay attacks and incompatible implementations.

The next section examines how this synchronized communication context is maintained throughout the lifetime of the connection using the Safety and Retransmission Layer state machine, heartbeat supervision, and deterministic protocol state transitions.

## 5.16 Safety and Retransmission Layer State Machine

Once the communication handshake has been completed successfully, the Safety and Retransmission Layer controls all subsequent protocol behaviour through a deterministic state machine. Every protocol event—whether initiated by the application, generated internally, or received from the communication partner—is processed according to predefined state transition rules specified in DIN VDE V 0831-200.

Unlike conventional communication protocols that may permit implementation-specific behaviour, RaSTA defines exactly one valid response for every combination of communication state and protocol event. This deterministic behaviour is essential for railway signalling systems because it ensures that independent implementations react identically under identical operating conditions.

The state machine of the Safety and Retransmission Layer is shown in Figure 5-4.

![Safety Layer State Machine](figures/figure-5-1-safety-layer-state-machine.png)

**Figure 5-4. State machine of the Safety and Retransmission Layer (adapted from DIN VDE V 0831-200 [1]).**

The state machine governs every stage of communication, including connection establishment, normal operation, retransmission, and safe disconnection. By defining explicit transitions between protocol states, the specification eliminates ambiguity and greatly simplifies protocol verification.

---

## 5.17 Communication States

The Safety and Retransmission Layer defines six operational states that together represent the complete lifecycle of a communication session.

| State | Description |
|--------|-------------|
| **Closed** | No communication session exists. |
| **Down** | Communication initialization is in progress. |
| **Start** | Connection establishment handshake is being executed. |
| **Up** | Normal communication is active. |
| **RetrReq** | Retransmission has been requested. |
| **RetrRun** | Missing messages are currently being retransmitted. |

**Table 5-6. Communication states of the Safety and Retransmission Layer.**

Communication always begins in the **Closed** state.

Following an application request to establish communication, the protocol initializes its internal variables and transitions to the **Down** and **Start** states while the connection establishment handshake is performed.

Once synchronization has been completed successfully, both communication partners enter the **Up** state.

The **Up** state represents normal protocol operation and remains active for the majority of the communication session.

Whenever sequence supervision detects missing protocol messages, communication temporarily enters the retransmission states (**RetrReq** and **RetrRun**) until synchronization has been restored.

If recovery fails or communication correctness can no longer be guaranteed, the protocol returns immediately to the **Closed** state.

---

## 5.18 Deterministic State Transitions

One of the defining characteristics of RaSTA is that protocol behaviour depends not only on the received message but also on the current communication state.

For example:

- A **Connection Request** is accepted only during connection establishment.
- **Application Data** is processed only while the protocol is in the **Up** state.
- **Retransmission Responses** are accepted only during retransmission.
- Messages received in inappropriate states are rejected immediately.

This state-dependent processing prevents invalid protocol sequences from influencing the signalling application.

Because every state transition is explicitly defined by the standard, different manufacturers implementing RaSTA will always process identical protocol events in the same manner.

This deterministic behaviour is particularly important for interoperability and safety certification, where predictable protocol behaviour is mandatory.

---

## 5.19 Heartbeat Supervision

During periods of normal communication, application messages naturally confirm that both communication partners remain operational.

However, signalling systems frequently experience periods during which no application data needs to be exchanged.

Without additional supervision, communication failures occurring during these idle periods could remain undetected.

To prevent this situation, RaSTA periodically transmits **Heartbeat (HB)** messages whenever the configurable heartbeat interval (**Th**) expires without application traffic.

Heartbeat messages do not contain application data. Instead, they carry the protocol control information required to continue communication supervision, including sequence numbers, timestamps, and message integrity protection.

Every valid Heartbeat received by the communication partner confirms that:

- communication remains operational;
- sequence synchronization is maintained;
- timing supervision remains valid;
- both protocol instances continue operating correctly.

Heartbeat messages therefore extend communication supervision throughout the entire lifetime of the connection, regardless of application traffic.

---

## 5.20 Adaptive Communication Monitoring

Heartbeat messages work together with the adaptive timing mechanism introduced earlier in the report.

Rather than using fixed timeout values, RaSTA continuously measures communication behaviour and adjusts its supervision timers according to observed network performance.

The protocol evaluates several timing parameters, including:

- transmission delay,
- processing delay,
- heartbeat interval,
- message age,
- communication drift.

This adaptive supervision enables the protocol to distinguish between temporary communication fluctuations and genuine communication failures.

Consequently, unnecessary protocol disconnects are avoided while genuine communication faults are detected rapidly.

Compared with conventional fixed-time supervision, this adaptive approach provides greater robustness when operating over modern packet-switched communication networks.

---

## 5.21 Functional Safety Contribution

The state machine and heartbeat supervision mechanisms provide the behavioural framework upon which the remaining RaSTA safety functions operate.

From a functional safety perspective they provide several important benefits:

- deterministic communication behaviour;
- continuous supervision during both active and idle communication;
- prevention of invalid protocol sequences;
- rapid detection of communication interruptions;
- predictable recovery following communication faults;
- safe transition to predefined protocol states.

Together these mechanisms ensure that communication remains continuously supervised throughout the lifetime of every connection.

Whenever communication correctness can no longer be demonstrated, the protocol intentionally leaves the operational state and transitions into a predefined safe condition.

This deterministic fail-safe behaviour represents one of the fundamental design principles of the RaSTA protocol.

---

## 5.22 Section Summary

This section examined the behavioural architecture of the Safety and Retransmission Layer. The protocol state machine defines every permitted communication state and explicitly specifies how protocol events shall be processed throughout the communication lifecycle.

Heartbeat supervision and adaptive timing complement the state machine by continuously monitoring communication availability, ensuring that failures are detected even during periods without application traffic.

These mechanisms establish the behavioural foundation upon which the remaining communication safety functions—sequence supervision, retransmission, flow control, and redundancy—operate.

## 5.23 Sequence Number Supervision

After communication has entered the **Up** state, every transmitted protocol message receives a unique **Sequence Number (SN)**. These sequence numbers enable the receiving communication partner to verify that messages are received exactly once and in the correct order.

Unlike conventional communication protocols that primarily use sequence numbers to improve reliability, RaSTA employs sequence supervision as a fundamental safety mechanism. Every received message is compared against the next expected sequence number before it is accepted by the application.

If the received sequence number matches the expected value, the message is processed normally. If a sequence discontinuity is detected, the protocol immediately concludes that communication has been disturbed and initiates the retransmission procedure.

Sequence supervision enables the protocol to detect several communication faults simultaneously, including:

- Message loss
- Message duplication
- Message replay
- Message resequencing
- Unexpected message insertion

By verifying every transmitted message, RaSTA ensures that only a complete and correctly ordered communication stream reaches the railway signalling application.

---

## 5.24 Confirmed Sequence Numbers

In addition to ordinary sequence numbers, every protocol message also contains a **Confirmed Sequence Number (CS)**.

Rather than transmitting dedicated acknowledgement packets, RaSTA acknowledges previously received messages by embedding the confirmed sequence number inside subsequent outgoing messages.

Three internal variables are maintained:

| Variable | Description |
|----------|-------------|
| **CST** | Confirmed sequence number transmitted to the communication partner |
| **CSR** | Most recent confirmed sequence number received |
| **CSPDU** | Confirmed sequence number contained within the received protocol message |

**Table 5-7. Confirmed sequence number variables used by the Safety and Retransmission Layer.**

Whenever a valid message is received, its sequence number is stored internally and later returned to the sender as part of the next outgoing protocol message.

This acknowledgement mechanism enables the sender to determine exactly which messages have already been received successfully and which messages remain buffered for possible retransmission.

The use of implicit acknowledgements reduces protocol overhead while maintaining deterministic communication behaviour.

---

## 5.25 Flow Control

Reliable communication requires that neither communication partner transmits messages faster than the other can process them.

RaSTA addresses this problem through a deterministic flow control mechanism based on the configuration parameter **Nsendmax**.

During connection establishment, both communication partners exchange their receive buffer capacities.

The sender continuously monitors the number of transmitted but unacknowledged protocol messages.

If this number reaches the receiver's advertised buffer capacity, further transmissions are temporarily suspended until acknowledgements become available.

Unlike conventional congestion control algorithms that adapt to network conditions, RaSTA flow control focuses on maintaining deterministic communication behaviour and preventing receiver buffer overflow.

Consequently, communication performance remains predictable even when the processing capabilities of both communication partners differ significantly.

---

## 5.26 Packetization

Many railway applications generate numerous small messages within short time intervals.

Sending each message individually would increase communication overhead and reduce transmission efficiency.

To improve bandwidth utilisation, RaSTA supports **packetization**, allowing multiple application messages to be combined into a single protocol data unit.

Each application message is preceded by its own length field, enabling the receiver to reconstruct the original message boundaries after reception.

The maximum number of application messages that may be combined is defined by the configuration parameter **NmaxPackage**.

Although packetization improves communication efficiency, it remains completely transparent to the signalling application.

After reception, the Safety and Retransmission Layer separates the individual application messages before forwarding them to the application software.

This design improves communication efficiency without affecting application behaviour or protocol safety.

---

## 5.27 Retransmission Mechanism

Despite the high reliability of modern railway communication networks, temporary packet loss cannot be completely eliminated.

Rather than immediately terminating communication whenever a message is lost, RaSTA attempts controlled recovery using its retransmission mechanism.

The retransmission procedure begins whenever the receiver detects a discontinuity in the sequence numbers.

Instead of forwarding incomplete information to the application, the receiver performs the following sequence:

1. Detect the missing sequence number.
2. Suspend application data delivery.
3. Generate a **Retransmission Request (RetrReq)**.
4. Wait for the sender's response.
5. Receive the missing application messages.
6. Restore sequence synchronization.
7. Resume normal communication.

Only after the missing messages have been received successfully does the protocol release the buffered application data.

This procedure guarantees that the application never observes incomplete communication.

---

## 5.28 Retransmission States

Retransmission introduces two dedicated protocol states.

| State | Description |
|--------|-------------|
| **RetrReq** | Waiting for retransmission after detecting a missing message. |
| **RetrRun** | Processing retransmitted messages while restoring synchronization. |

**Table 5-8. Retransmission states of the Safety and Retransmission Layer.**

These states temporarily interrupt normal application communication while protocol consistency is restored.

If retransmission completes successfully, communication returns automatically to the **Up** state.

If retransmission cannot be completed before the adaptive supervision timer expires, communication is terminated safely.

This behaviour demonstrates one of the fundamental design principles of RaSTA:

> **Attempt recovery whenever communication can still be restored safely. Otherwise, terminate communication before unsafe information reaches the signalling application.**

---

## 5.29 Functional Safety Contribution

The mechanisms presented in this section operate together to preserve communication consistency throughout the complete communication session.

Sequence supervision detects communication disturbances.

Confirmed sequence numbers acknowledge successful reception.

Flow control prevents communication overload.

Packetization improves transmission efficiency.

Retransmission restores temporarily lost information while preventing incomplete communication from reaching the application.

Collectively, these mechanisms provide an additional layer of protection that complements the state machine and heartbeat supervision analysed previously.

Instead of assuming perfect communication, RaSTA continuously validates every transmitted message and performs corrective actions whenever inconsistencies are detected.

This layered verification strategy significantly improves both communication reliability and functional safety.

## 5.30 Redundancy Layer

The Safety and Retransmission Layer guarantees communication correctness. However, communication correctness alone is not sufficient for railway signalling systems. High operational availability is equally important because unnecessary communication interruptions may reduce railway capacity and system performance.

For this reason, RaSTA introduces a dedicated **Redundancy Layer** whose objective is to improve communication availability without affecting the safety mechanisms implemented by the upper protocol layer.

Unlike the Safety and Retransmission Layer, the Redundancy Layer does not verify application data or communication integrity. Instead, it provides a reliable transport service by managing multiple independent communication channels and presenting them as a single logical channel to the upper protocol layer.

This clear separation of responsibilities allows safety and availability to be addressed independently while maintaining a modular protocol architecture.

---

## 5.31 Channel Model

The redundancy concept implemented by RaSTA is illustrated by the channel model shown below.

![Redundancy Channel Model](figures/figure-5-2-redundancy-channel-model.png)

**Figure 5-5. Channel model of the Redundancy Layer (adapted from DIN VDE V 0831-200 [1]).**

Instead of transmitting messages over only one physical communication channel, the Redundancy Layer simultaneously forwards identical protocol messages through every configured transport channel.

Examples include:

- Ethernet Channel A
- Ethernet Channel B
- Optical Fibre
- Wireless Communication

At the receiving side, duplicate protocol messages are recognised using redundancy sequence numbers.

The first correctly received message is immediately forwarded to the Safety and Retransmission Layer.

Additional copies arriving later are discarded after diagnostic evaluation.

Consequently, the upper protocol layers remain completely independent of the physical communication infrastructure.

---

## 5.32 Event-State Matrix

The behaviour of the Redundancy Layer is formally defined by an event-state matrix.

![Redundancy Event-State Matrix](figures/figure-5-3-redundancy-event-state-matrix.png)

**Figure 5-6. Event-state matrix of the Redundancy Layer (adapted from DIN VDE V 0831-200 [1]).**

The matrix specifies how every incoming protocol event shall be processed depending on the current communication state.

Unlike the Safety and Retransmission Layer, which manages several operational states, the Redundancy Layer performs only a limited number of state transitions.

Nevertheless, every event is still processed deterministically.

This deterministic processing guarantees that different RaSTA implementations will react identically under identical communication conditions.

---

## 5.33 Redundancy State Machine

The operational behaviour of the Redundancy Layer is illustrated by its state machine.

![Redundancy State Machine](figures/figure-5-4-redundancy-state-machine.png)

**Figure 5-7. State transitions of the Redundancy Layer (adapted from DIN VDE V 0831-200 [1]).**

Only two operational states are defined.

| State | Description |
|--------|-------------|
| **Closed** | No redundancy channel exists. |
| **Up** | Redundancy communication is active. |

**Table 5-9. Operational states of the Redundancy Layer.**

The simplicity of the redundancy state machine reflects the specialised purpose of this protocol layer.

Rather than supervising communication correctness, the Redundancy Layer concentrates exclusively on providing highly available communication services for the Safety and Retransmission Layer.

---

## 5.34 Principal Redundancy Functions

The behaviour of the Redundancy Layer is implemented through several internal functions defined by DIN VDE V 0831-200.

These functions are presented in the following figures.

### Initialization

![Function f_init()](figures/figure-5-5-redundancy-function-f-init.png)

**Figure 5-8. Function `f_init()` (adapted from DIN VDE V 0831-200 [1]).**

The `f_init()` function creates a new redundancy context, initializes sequence numbers, clears internal queues, configures timers, and prepares the communication channels before data transmission begins.

---

### Cleanup

![Function f_cleanup()](figures/figure-5-6-redundancy-function-f-cleanup.png)

**Figure 5-9. Function `f_cleanup()` (adapted from DIN VDE V 0831-200 [1]).**

When communication terminates, the `f_cleanup()` function releases all allocated protocol resources, resets timers, clears buffered messages, and returns the redundancy layer to the Closed state.

---

### Receiving Data

![Function f_receiveData()](figures/figure-5-7-redundancy-function-f-receive-data.png)

**Figure 5-10. Function `f_receiveData()` (adapted from DIN VDE V 0831-200 [1]).**

The `f_receiveData()` function validates incoming redundancy messages, verifies redundancy sequence numbers, detects duplicates, and determines whether received messages should be forwarded immediately or temporarily stored inside the Defer Queue.

This function forms the principal decision point of the Redundancy Layer.

---

### Sending Data

![Function f_sendData()](figures/figure-5-8-redundancy-function-f-send-data.png)

**Figure 5-11. Function `f_sendData()` (adapted from DIN VDE V 0831-200 [1]).**

The `f_sendData()` function duplicates every outgoing protocol message and transmits identical copies through every configured transport channel.

By distributing messages across independent communication paths, the protocol significantly increases communication availability without modifying the behaviour of the Safety Layer.

---

### Defer Queue Timeout

![Function f_deferTmo()](figures/figure-5-9-redundancy-function-f-defer-tmo.png)

**Figure 5-12. Function `f_deferTmo()` (adapted from DIN VDE V 0831-200 [1]).**

Messages arriving earlier than expected are stored temporarily inside the Defer Queue.

The `f_deferTmo()` function supervises the associated timeout (**Tseq**).

If the missing message does not arrive before the timeout expires, the queued message is released to the upper protocol layer according to the redundancy rules defined by the specification.

---

### Delivering Deferred Messages

![Function f_deliverDeferQueue()](figures/figure-5-10-redundancy-function-f-deliver-defer-queue.png)

**Figure 5-13. Function `f_deliverDeferQueue()` (adapted from DIN VDE V 0831-200 [1]).**

Whenever the expected message arrives successfully, the `f_deliverDeferQueue()` function removes the stored messages from the Defer Queue and forwards them to the Safety and Retransmission Layer in the correct order.

This mechanism allows the Redundancy Layer to compensate for temporary differences in transport channel latency while preserving deterministic message sequencing.

---

## 5.35 Contribution of the Redundancy Layer to Functional Safety

Although the Redundancy Layer primarily improves communication availability, it also contributes indirectly to functional safety.

Multiple communication channels reduce the probability that a single transport failure interrupts communication.

The Defer Queue preserves correct message ordering despite differences in transport latency.

Duplicate detection prevents multiple copies of the same message from reaching the Safety Layer.

Most importantly, the Redundancy Layer performs these functions transparently.

The Safety and Retransmission Layer continues operating exactly as if only one communication channel existed.

This separation allows both protocol layers to remain relatively simple while collectively providing both communication safety and high availability.

---

## 5.36 Chapter Summary

This chapter examined the complete architecture of the Rail Safe Transport Application protocol.

The analysis began with the layered organisation of the protocol before examining the Safety and Retransmission Layer, protocol data units, connection establishment, deterministic state management, heartbeat supervision, sequence verification, retransmission, and finally the Redundancy Layer.

The architecture demonstrates one of the strongest characteristics of RaSTA: the separation of communication safety from communication availability.

The Safety and Retransmission Layer ensures that only correct information reaches the railway signalling application, while the Redundancy Layer increases communication robustness by managing multiple transport channels transparently.

Together these layers provide a modular, deterministic, and highly dependable communication architecture that satisfies the functional safety objectives required for modern railway signalling systems.

The following chapter analyses the operational scenarios defined by DIN VDE V 0831-200 to demonstrate that these architectural mechanisms operate correctly during both normal and fault conditions.
# 6. Proof of Correct Functional Operation

## 6.1 Introduction

The previous chapter examined the architecture of the Rail Safe Transport Application (RaSTA) protocol and explained the mechanisms responsible for ensuring communication safety and availability. While these architectural components describe how the protocol is constructed, they do not by themselves demonstrate that the protocol behaves correctly during real communication.

Functional safety requires evidence that the implemented mechanisms operate correctly under both normal and abnormal operating conditions. For this reason, DIN VDE V 0831-200 provides a number of operational scenarios illustrating how the protocol reacts during connection establishment, communication delays, retransmission, protocol errors, and communication failures.

These scenarios are especially valuable because they demonstrate the interaction between multiple protocol mechanisms. Sequence supervision, heartbeat monitoring, adaptive timing, retransmission, and deterministic state transitions work together to detect, isolate, and recover from communication faults before incorrect information reaches the signalling application.

This chapter analyses the operational scenarios defined by the standard and evaluates how they provide evidence that the protocol satisfies its functional safety objectives.

---

## 6.2 Functional Verification Strategy

Unlike many communication protocols that focus primarily on performance, RaSTA continuously verifies communication correctness throughout the lifetime of every connection.

The protocol evaluates several independent properties simultaneously:

- Message integrity
- Sequence correctness
- Communication timing
- Sender authenticity
- Receiver authenticity
- Connection state
- Communication availability

Only when every verification step is successful is the received message delivered to the signalling application.

This layered verification strategy significantly reduces the probability that communication faults remain undetected.

The operational scenarios analysed in this chapter demonstrate how these verification mechanisms behave during realistic communication situations.

---

## 6.3 Successful Connection Establishment

The first operational scenario illustrates successful initialization of communication between two RaSTA instances.

![Successful Connection Establishment](figures/figure-6-1-successful-connection-establishment.png)

**Figure 6-1. Successful connection establishment (adapted from DIN VDE V 0831-200 [1]).**

The communication process begins when the client transmits a **Connection Request (ConnReq)**.

The server validates the received request and responds with a **Connection Response (ConnResp)** containing its own communication parameters.

Finally, the client transmits a **Heartbeat (HB)** message confirming that communication synchronization has been completed successfully.

Only after these three protocol messages have been exchanged do both communication partners enter the **Up** state.

At this point, application messages may be exchanged safely.

---

## 6.4 Step-by-Step Analysis

The communication procedure shown in Figure 6-1 can be analysed in four distinct stages.

### Stage 1 – Communication Request

The initiating communication partner sends a Connection Request containing:

- sender identifier,
- receiver identifier,
- protocol version,
- random sequence number,
- timing information,
- receive buffer size.

The receiving communication partner validates every field before continuing.

---

### Stage 2 – Parameter Verification

After successful validation, the receiver creates a Connection Response.

During this step both communication partners verify:

- protocol compatibility,
- communication identifiers,
- receive buffer capacities,
- sequence number initialization,
- message integrity.

If any inconsistency is detected, communication is terminated immediately.

---

### Stage 3 – Synchronization

The client transmits the first Heartbeat message.

Although Heartbeat messages later supervise idle communication, the first Heartbeat has a special purpose.

It confirms that both communication partners have completed initialization successfully and now possess identical protocol contexts.

---

### Stage 4 – Normal Communication

Following successful synchronization, both communication partners transition into the **Up** state.

Normal application communication may now begin.

During this operational phase, every transmitted message continues to perform communication supervision through sequence numbers, timestamps, acknowledgements, and integrity verification.

---

## 6.5 Functional Safety Assessment

The connection establishment scenario demonstrates several important functional safety principles.

First, communication is never established immediately after receiving a Connection Request.

Instead, both communication partners independently verify protocol compatibility before entering operational communication.

Second, random sequence numbers prevent protocol messages belonging to previous communication sessions from being accepted during new communication sessions.

Third, synchronization is not considered complete until the first Heartbeat has been exchanged successfully.

Finally, deterministic state transitions ensure that communication proceeds identically in every compliant RaSTA implementation.

Together these mechanisms establish a trusted communication context before safety-related information is exchanged.

---

## 6.6 Verification Results

The successful connection establishment scenario demonstrates that the protocol satisfies several fundamental communication safety objectives.

| Verification Objective | Result |
|------------------------|--------|
| Protocol compatibility verified | ✓ |
| Sender and receiver identified | ✓ |
| Sequence numbers synchronized | ✓ |
| Adaptive timing initialized | ✓ |
| Communication buffers negotiated | ✓ |
| Safe transition into Up state | ✓ |

**Table 6-1. Functional verification performed during successful connection establishment.**

The analysed scenario demonstrates that communication begins only after every required protocol parameter has been verified successfully.

This conservative initialization procedure significantly reduces the probability that communication faults influence subsequent protocol behaviour.

---

## 6.7 Section Summary

The first operational scenario demonstrates that RaSTA establishes communication through a carefully controlled synchronization procedure rather than a simple connection request.

Every stage of the handshake contributes directly to communication safety by verifying protocol compatibility, synchronizing communication parameters, and confirming that both protocol instances have entered identical operational states.

The following section analyses abnormal connection establishment scenarios, including delayed protocol messages and protocol incompatibilities, to demonstrate how RaSTA behaves when communication initialization cannot be completed successfully.

## 6.8 Delayed Connection Response

Successful communication requires both communication partners to complete the connection establishment procedure within predefined timing limits. If the expected **Connection Response (ConnResp)** does not arrive within the maximum permissible time, the protocol assumes that communication cannot be established safely.

The following scenario illustrates this situation.

![Delayed Connection Response](figures/figure-6-2-delayed-connection-response.png)

**Figure 6-2. Delayed Connection Response during connection establishment (adapted from DIN VDE V 0831-200 [1]).**

After transmitting the Connection Request, the initiating communication partner starts a supervision timer.

Normally, the receiver validates the request and immediately returns a Connection Response. However, if the response is delayed because of communication failures, excessive network latency, or receiver malfunction, the supervision timer eventually expires.

Rather than waiting indefinitely, the client terminates the connection attempt and returns to the **Closed** state.

No application data is exchanged because synchronization between both communication partners has not yet been completed.

---

### Technical Analysis

The timeout mechanism serves several important engineering purposes.

First, it prevents protocol resources from remaining allocated indefinitely during failed connection attempts.

Second, it avoids communication using incomplete synchronization information.

Finally, it guarantees deterministic behaviour because every implementation responds identically whenever the timeout expires.

The timeout therefore acts as an additional communication safety barrier during protocol initialization.

---

### Functional Safety Assessment

From a functional safety perspective, delaying communication is preferable to accepting uncertain communication.

If a delayed Connection Response were accepted after the protocol context had already become invalid, communication partners could begin exchanging messages using inconsistent sequence numbers or outdated timing information.

By immediately terminating unsuccessful connection attempts, RaSTA ensures that every communication session begins from a fully synchronized state.

---

## 6.9 Delayed Heartbeat

The final stage of connection establishment requires transmission of the first **Heartbeat (HB)** message.

Although Heartbeat messages later supervise idle communication, the first Heartbeat has a special role because it confirms successful synchronization between both communication partners.

The following scenario demonstrates communication failure caused by an excessive Heartbeat delay.

![Delayed Heartbeat](figures/figure-6-3-delayed-heartbeat.png)

**Figure 6-3. Delayed Heartbeat during connection establishment (adapted from DIN VDE V 0831-200 [1]).**

After receiving the Connection Response, the server waits for the synchronization Heartbeat generated by the client.

If the Heartbeat arrives within the configured supervision interval, communication enters the **Up** state.

However, if the Heartbeat is delayed beyond the permitted timeout, the synchronization procedure remains incomplete.

The server therefore concludes that communication cannot be trusted and immediately terminates the connection establishment procedure.

Both communication partners return to the **Closed** state.

---

### Technical Analysis

The first Heartbeat performs two independent functions.

It confirms that the client has received and accepted the Connection Response.

It also verifies that both communication partners have entered identical protocol states before application communication begins.

Without this confirmation, the server cannot distinguish between successful initialization and communication failure.

Consequently, the synchronization Heartbeat provides the final verification step before operational communication.

---

### Functional Safety Assessment

This scenario demonstrates the conservative design philosophy adopted throughout RaSTA.

Rather than assuming that communication has been established successfully after transmitting the Connection Response, the protocol requires explicit confirmation from the communication partner.

Only after receiving this confirmation does communication become operational.

This approach significantly reduces the probability of partially initialized communication sessions.

---

## 6.10 Client-Detected Protocol Error

Another important operational scenario concerns protocol errors detected during connection establishment.

The following figure illustrates communication termination after the client detects an invalid Connection Response.

![Client Detected Protocol Error](figures/figure-6-4-client-detected-protocol-error.png)

**Figure 6-4. Client-detected protocol error during connection establishment (adapted from DIN VDE V 0831-200 [1]).**

After receiving the Connection Response, the client performs several verification procedures before accepting the communication session.

Typical verification steps include:

- Protocol version compatibility
- Sender identification
- Receiver identification
- Security Code verification
- Message format verification
- Sequence number validation

If any verification step fails, the Connection Response is rejected immediately.

Instead of attempting communication using uncertain protocol parameters, the client generates a **Disconnect Request** and terminates the communication session.

Communication therefore returns directly to the **Closed** state.

---

### Technical Analysis

Unlike many general-purpose communication protocols, RaSTA performs protocol verification before communication becomes operational.

Every field contained within the Connection Response contributes to communication correctness.

If one parameter differs from the expected value, deterministic communication can no longer be guaranteed.

For this reason, the protocol rejects the complete communication session instead of attempting partial recovery.

---

### Functional Safety Assessment

Protocol compatibility is fundamental to functional safety.

Even small implementation differences could result in two communication partners interpreting identical protocol messages differently.

Such inconsistent behaviour would violate the deterministic operation required by railway signalling systems.

By validating every protocol parameter before entering the operational state, RaSTA prevents incompatible implementations from exchanging safety-related information.

---

## 6.11 Evaluation of Connection Establishment Failures

The three failure scenarios analysed in this section demonstrate that RaSTA never attempts to establish communication under uncertain conditions.

Instead, communication is permitted only after all synchronization procedures have completed successfully.

The analysed scenarios show that the protocol correctly detects:

- delayed communication,
- missing synchronization,
- incompatible protocol implementations,
- invalid protocol parameters.

Whenever one of these conditions occurs, the protocol terminates communication before application data can be exchanged.

This behaviour reflects the fail-safe philosophy of railway communication systems, where communication uncertainty is considered more hazardous than temporary communication interruption.

---

## 6.12 Section Summary

The abnormal connection establishment scenarios provide further evidence that RaSTA satisfies the functional safety principles defined by DIN EN 50159.

Delayed protocol messages, missing synchronization, and protocol incompatibilities are all detected before operational communication begins.

Rather than attempting uncertain recovery, the protocol safely terminates communication and returns to the **Closed** state.

The next section analyses normal operational communication, including application data exchange, successful retransmission, and failed retransmission, demonstrating how RaSTA maintains communication safety throughout the lifetime of an established connection.

## 6.13 Normal Data Exchange

After successful completion of the connection establishment procedure, both communication partners enter the **Up** state. This state represents normal protocol operation, where application data can be exchanged while the protocol continuously supervises communication.

Unlike conventional transport protocols, RaSTA does not separate application communication from communication supervision. Every transmitted protocol message contributes simultaneously to information transfer, message acknowledgement, sequence verification, and timing supervision.

The normal communication process is illustrated in Figure 6-5.

![Normal Data Exchange](figures/figure-6-5-normal-data-exchange.png)

**Figure 6-5. Normal exchange of application data during the Up state (adapted from DIN VDE V 0831-200 [1]).**

During normal operation, application messages are transmitted whenever new signalling information becomes available.

Each protocol message contains:

- Application data
- Sequence Number (SN)
- Confirmed Sequence Number (CS)
- Timestamp (TS)
- Confirmed Timestamp (CTS)
- Security Code

The receiving communication partner validates every field before delivering the application data.

Whenever a valid protocol message is received, the receiver:

1. Verifies the Security Code.
2. Checks the sender and receiver identifiers.
3. Verifies the sequence number.
4. Updates the adaptive timing information.
5. Stores the confirmed sequence number.
6. Delivers the application data.

If no application messages are available before the heartbeat timer expires, a Heartbeat message is transmitted automatically.

Heartbeat messages therefore guarantee continuous communication supervision even during idle communication periods.

---

### Engineering Analysis

This communication model provides several advantages.

Application traffic and communication supervision are integrated into a single protocol message.

Acknowledgements are transmitted implicitly through Confirmed Sequence Numbers.

Communication timing is continuously monitored without generating unnecessary protocol traffic.

Consequently, communication safety is maintained with relatively low communication overhead.

---

### Functional Safety Assessment

The normal communication scenario demonstrates that communication supervision is continuous rather than event-based.

Every protocol message contributes simultaneously to:

- message integrity verification;
- sequence supervision;
- timing supervision;
- acknowledgement processing;
- communication availability.

This integrated design significantly reduces the probability that communication faults remain undetected.

---

## 6.14 Successful Retransmission

Despite the high reliability of railway communication networks, occasional packet loss cannot be completely eliminated.

Rather than immediately terminating communication whenever a message is lost, RaSTA first attempts controlled recovery using its retransmission mechanism.

The following operational scenario illustrates successful retransmission.

![Successful Retransmission](figures/figure-6-6-successful-retransmission.png)

**Figure 6-6. Successful retransmission after the loss of a protocol message (adapted from DIN VDE V 0831-200 [1]).**

In this example, one protocol message is lost during transmission.

The receiver subsequently receives the following message.

Because the received Sequence Number does not match the expected value, the protocol immediately detects the missing message.

Instead of forwarding incomplete application data to the signalling application, communication proceeds through the following sequence:

1. Detect missing Sequence Number.
2. Enter the **RetrReq** state.
3. Generate a **Retransmission Request (RetrReq)**.
4. Wait for the sender's response.
5. Receive the missing messages as **RetrData**.
6. Restore sequence synchronization.
7. Return automatically to the **Up** state.

Only after all missing messages have been received successfully does the protocol release the buffered application data.

---

### Engineering Analysis

Retransmission allows communication to recover from temporary network disturbances without interrupting the communication session.

The sender maintains a retransmission buffer containing previously transmitted messages.

Whenever a Retransmission Request is received, the sender identifies the missing sequence numbers and retransmits only the required messages.

This selective retransmission reduces communication overhead while restoring protocol synchronization efficiently.

---

### Functional Safety Assessment

Successful retransmission demonstrates one of the most important design objectives of RaSTA.

Temporary communication failures do not immediately result in communication loss.

Instead, the protocol first attempts deterministic recovery while preventing incomplete information from reaching the application.

Communication availability therefore increases significantly without reducing communication safety.

---

## 6.15 Failed Retransmission

Not every communication disturbance can be corrected successfully.

The following operational scenario illustrates communication failure during retransmission.

![Failed Retransmission](figures/figure-6-7-failed-retransmission.png)

**Figure 6-7. Failed retransmission resulting in safe communication termination (adapted from DIN VDE V 0831-200 [1]).**

Initially, communication proceeds exactly as in the successful retransmission scenario.

A missing protocol message is detected and the retransmission procedure begins normally.

However, during retransmission another protocol message is also lost.

Consequently, the receiver is unable to reconstruct the complete communication sequence.

Meanwhile, the adaptive supervision timer continues monitoring communication delay.

Eventually the maximum permitted communication time is exceeded.

Because communication correctness can no longer be guaranteed, the protocol terminates the communication session.

Both communication partners return to the **Closed** state.

---

### Engineering Analysis

The retransmission mechanism is intentionally limited.

Recovery is attempted only while communication timing remains within the limits defined by the adaptive supervision algorithm.

Once these limits are exceeded, further recovery attempts are abandoned because delayed application information may no longer represent the current railway system state.

The protocol therefore favours communication correctness over prolonged recovery attempts.

---

### Functional Safety Assessment

This scenario clearly demonstrates the fail-safe philosophy of RaSTA.

The protocol attempts recovery whenever safe recovery remains possible.

However, recovery is never allowed to continue indefinitely.

Once communication freshness can no longer be guaranteed, the protocol intentionally disconnects rather than risking delivery of outdated signalling information.

This behaviour directly satisfies the communication safety principles defined by DIN EN 50159.

---

## 6.16 Operational Behaviour Analysis

The three operational scenarios analysed in this section demonstrate that RaSTA behaves predictably throughout normal communication.

During normal operation:

- communication is continuously supervised;
- application messages are verified before delivery;
- heartbeat messages maintain supervision during idle periods.

When communication disturbances occur:

- missing messages are detected immediately;
- retransmission restores communication whenever possible;
- communication is terminated safely whenever recovery becomes impossible.

These scenarios demonstrate that RaSTA combines deterministic protocol behaviour with controlled fault recovery, providing both high communication availability and strong functional safety.

## 6.17 Verification Against Functional Safety Objectives

The operational scenarios analysed throughout this chapter demonstrate that the Rail Safe Transport Application (RaSTA) protocol behaves deterministically under both normal and abnormal communication conditions. Rather than assuming that the underlying communication network behaves correctly, the protocol continuously evaluates communication integrity throughout the lifetime of every connection.

Each operational scenario illustrates the interaction of multiple protocol mechanisms. Sequence number supervision, adaptive timing, heartbeat monitoring, retransmission, and deterministic state transitions operate together to detect communication faults before incorrect information reaches the signalling application.

From a functional safety perspective, these mechanisms provide two complementary objectives:

- **Communication Integrity**, ensuring that only correct and complete information reaches the application.
- **Communication Availability**, ensuring that temporary communication faults are recovered whenever safe recovery remains possible.

The combination of these objectives allows RaSTA to provide dependable communication without compromising railway safety.

---

## 6.18 Verification Against DIN EN 50159

DIN EN 50159 identifies several communication threats that must be considered whenever safety-related information is transmitted through an open or shared communication network.

The operational behaviour demonstrated in the previous scenarios provides evidence that RaSTA addresses each of these threats through dedicated protocol mechanisms.

| Communication Threat (DIN EN 50159) | RaSTA Mechanism | Operational Evidence |
|-------------------------------------|-----------------|----------------------|
| Message corruption | Security Code verification | Invalid messages are rejected before delivery. |
| Message loss | Retransmission | Lost messages are recovered whenever possible. |
| Message duplication | Sequence Number supervision | Duplicate messages are discarded automatically. |
| Message resequencing | Sequence Number verification | Messages are delivered only in the correct order. |
| Replay of old messages | Random initial Sequence Numbers | Messages from previous communication sessions are rejected. |
| Excessive communication delay | Adaptive timing supervision | Communication is terminated when timing limits are exceeded. |
| Communication interruption | Heartbeat supervision | Missing communication is detected automatically. |
| Incompatible implementations | Protocol version negotiation | Communication is refused before entering the Up state. |

**Table 6-2. Relationship between DIN EN 50159 communication threats and the RaSTA operational mechanisms.**

The analysis shows that no communication threat is mitigated by only one protocol function. Instead, several independent mechanisms cooperate to provide layered protection against communication failures.

This multi-layered design significantly increases confidence in the overall safety of the communication system.

---

## 6.19 Discussion

The operational scenarios reveal several important engineering characteristics of the RaSTA protocol.

First, protocol behaviour remains completely deterministic. Every communication event produces one clearly defined response specified by the standard, allowing independent implementations to behave identically under identical operating conditions.

Second, communication supervision remains active throughout the entire communication session. Unlike traditional transport protocols, which often verify communication only during connection establishment or error recovery, RaSTA continuously validates message integrity, sequence numbers, timing information, and communication state.

Third, the protocol follows a conservative recovery strategy. Temporary communication failures are corrected using retransmission whenever safe recovery remains possible. However, once communication freshness or synchronization can no longer be guaranteed, the protocol abandons recovery and safely terminates the communication session.

Finally, the operational scenarios demonstrate the complementary relationship between the Safety and Retransmission Layer and the Redundancy Layer. While retransmission restores missing information, redundancy reduces the likelihood that retransmission becomes necessary by increasing communication availability through multiple transport channels.

Together these mechanisms provide a balanced communication strategy that simultaneously supports reliability, availability, and functional safety.

---

## 6.20 Lessons Learned

The analysis of the operational scenarios demonstrates that functional safety cannot be achieved through a single protective mechanism.

Instead, RaSTA combines several independent safety functions that continuously monitor different aspects of communication.

These include:

- Message integrity verification
- Sender and receiver identification
- Sequence number supervision
- Adaptive timing supervision
- Heartbeat monitoring
- Controlled retransmission
- Deterministic protocol state management
- Communication redundancy

Each mechanism addresses a different category of communication failure. Collectively they create a layered defence strategy capable of detecting, correcting, or safely containing communication faults before they influence railway signalling applications.

This approach reflects the principles of modern functional safety engineering, where multiple independent safety barriers provide significantly greater confidence than reliance on any individual protection mechanism.

---

## 6.21 Chapter Summary

This chapter evaluated the operational behaviour of the Rail Safe Transport Application protocol using the communication scenarios defined by DIN VDE V 0831-200.

The analysis demonstrated that RaSTA behaves deterministically during successful communication, delayed communication, protocol incompatibilities, retransmission, and communication failures. Throughout every scenario, the protocol continuously verifies communication correctness using sequence supervision, message integrity verification, adaptive timing, heartbeat monitoring, and deterministic state transitions.

When temporary communication disturbances occur, controlled retransmission restores synchronization without exposing incomplete information to the application. When safe recovery is no longer possible, the protocol intentionally terminates communication before outdated or uncertain information can influence railway signalling functions.

The operational evidence presented in this chapter therefore supports the conclusion that RaSTA satisfies the communication safety principles defined by DIN EN 50159 while maintaining the high level of availability required for modern railway signalling systems.

The following chapter develops the overall **Safety Case**, combining the architectural analysis and operational evidence presented throughout this report into a structured engineering argument demonstrating why the protocol can be considered suitable for safety-related railway communication.
# 7. Safety Case

## 7.1 Introduction

The previous chapters analysed the architecture, operational behaviour, and mission-critical mechanisms of the Rail Safe Transport Application (RaSTA) protocol. Collectively, these analyses provide evidence that the protocol behaves deterministically and detects communication faults before they can influence railway signalling applications.

However, demonstrating that individual protocol mechanisms operate correctly is not sufficient to establish confidence in a safety-related communication protocol. A functional safety assessment must also demonstrate that these mechanisms collectively satisfy the safety objectives defined for the system.

This chapter develops a structured safety case for RaSTA by evaluating how the protocol addresses the communication hazards identified in DIN EN 50159. The objective is not to prove that communication failures cannot occur, but rather to demonstrate that communication failures are either detected, corrected, or transformed into safe system behaviour before unsafe information reaches the application.

---

# 7.2 Safety Objective

The primary safety objective of RaSTA is straightforward:

> **No incorrect, incomplete, delayed, duplicated or unauthorised message shall influence the behaviour of the railway signalling application.**

Unlike general-purpose communication protocols, RaSTA is not designed simply to maximise throughput or minimise latency. Instead, every design decision is evaluated according to its contribution to functional safety.

Consequently, the protocol follows three fundamental engineering principles:

- Detect communication faults.
- Recover safely whenever possible.
- Enter a predefined safe state whenever safe recovery is impossible.

These principles define the overall safety philosophy of the protocol.

---

# 7.3 Communication Hazards

DIN EN 50159 identifies several hazards that may arise when transmitting safety-related information through communication networks.

These hazards are summarised in Table 7-1.

| Communication Hazard | Potential Consequence |
|----------------------|-----------------------|
| Message corruption | Incorrect control information reaches the signalling system. |
| Message loss | Required control information is not received. |
| Message duplication | Commands may be executed multiple times. |
| Replay | Obsolete information may be interpreted as current information. |
| Message resequencing | Incorrect operating sequence may be generated. |
| Excessive delay | Information may no longer represent the current system state. |
| Communication interruption | Loss of communication between signalling components. |

**Table 7-1. Principal communication hazards identified by DIN EN 50159.**

Each of these hazards has the potential to affect the safe operation of railway signalling equipment if not detected before application processing.

---

# 7.4 Safety Argument

The RaSTA protocol addresses these hazards using multiple independent safety mechanisms rather than relying on a single communication safeguard.

The relationship between hazards and mitigation mechanisms is illustrated in Table 7-2.

| Hazard | Safety Mechanism | Result |
|----------|-----------------|--------|
| Corruption | MD4 Security Code | Corrupted messages are rejected. |
| Loss | Retransmission | Missing messages are recovered. |
| Duplication | Sequence Number Verification | Duplicate messages are discarded. |
| Replay | Random Initial Sequence Numbers | Previous communication sessions cannot be reused. |
| Resequencing | Sequence Supervision | Messages are delivered in the correct order. |
| Delay | Adaptive Channel Monitoring | Outdated information is rejected. |
| Interruption | Heartbeat Supervision | Communication failure is detected automatically. |
| Buffer Overflow | Flow Control | Communication remains deterministic. |
| Transport Failure | Redundancy Layer | Communication availability increases. |

**Table 7-2. Relationship between communication hazards and RaSTA safety mechanisms.**

The table demonstrates that each communication hazard is addressed by one or more independent protocol mechanisms. In several cases, multiple mechanisms contribute to the same safety objective, providing redundancy within the safety architecture itself.

---

# 7.5 Layered Defence Strategy

One of the strongest characteristics of RaSTA is its layered defence strategy.

Instead of relying on a single verification step, every received message passes through several independent validation procedures before reaching the signalling application.

These include:

1. Message integrity verification.
2. Sender and receiver validation.
3. Sequence verification.
4. Timestamp verification.
5. State machine validation.
6. Adaptive timing supervision.

Only if every validation stage is completed successfully is the message delivered to the application.

If any verification step fails, the protocol either initiates recovery or safely terminates the communication session.

This layered verification strategy significantly reduces the probability that multiple independent communication failures remain undetected.

---

# 7.6 Fail-Safe Behaviour

Functional safety does not require communication to remain available under all circumstances.

Instead, it requires communication to behave predictably whenever faults occur.

RaSTA follows a strict fail-safe philosophy.

Whenever the protocol determines that communication correctness can no longer be guaranteed, it intentionally disconnects the communication session.

Examples include:

- communication timeout;
- incompatible protocol versions;
- unrecoverable retransmission failure;
- invalid message integrity;
- protocol state violations.

Although disconnecting communication may temporarily reduce railway availability, it prevents uncertain communication from influencing safety-critical control functions.

This behaviour is consistent with the principles of fail-safe system design adopted throughout railway engineering.

---

# 7.7 Evidence Supporting the Safety Case

The safety argument presented throughout this report is supported by multiple forms of evidence.

| Evidence | Presented In |
|-----------|--------------|
| Protocol architecture | Section 2 |
| Communication requirements | Section 3 |
| Mission-critical mechanisms | Section 4 |
| Protocol architecture and state machines | Section 5 |
| Operational scenarios | Section 6 |
| DIN VDE V 0831-200 specification | Reference [1] |
| DIN EN 50159 communication principles | Reference [2] |

**Table 7-3. Evidence supporting the functional safety argument.**

Together, these analyses demonstrate that RaSTA satisfies its intended communication safety objectives through deterministic protocol behaviour and multiple independent protection mechanisms.

---

# 7.8 Limitations

Although RaSTA provides comprehensive communication safety, it is important to recognise its scope.

The protocol guarantees the safety of communication between applications; however, it does not guarantee that the applications themselves are functionally correct.

Similarly, RaSTA assumes that:

- the signalling application has been correctly designed;
- the underlying hardware satisfies its safety requirements;
- system configuration parameters are selected correctly;
- communication channels satisfy the assumptions defined by the standard.

Therefore, RaSTA represents one component within a complete railway functional safety architecture rather than a complete safety solution on its own.

---

# 7.9 Chapter Summary

This chapter developed a structured safety case demonstrating how the Rail Safe Transport Application protocol satisfies the communication safety objectives defined by DIN EN 50159.

Rather than relying on a single communication safeguard, RaSTA employs multiple independent protection mechanisms including integrity verification, sequence supervision, adaptive timing, heartbeat monitoring, retransmission, redundancy, and deterministic state management. These mechanisms collectively detect, recover from, or safely contain communication faults before they influence railway signalling applications.

The analysis demonstrates that the protocol follows a clear fail-safe philosophy. Whenever communication correctness cannot be guaranteed, the protocol intentionally transitions to a predefined safe state rather than risking unsafe operation. This layered defence strategy provides strong evidence that RaSTA is suitable for safety-related railway communication systems requiring high levels of integrity, availability, and deterministic behaviour.
# 8. Summary

This report presented a functional safety analysis of the Rail Safe Transport Application (RaSTA) protocol based on the requirements and protocol specification defined in DIN VDE V 0831-200. The objective was to evaluate how the protocol ensures safe, deterministic, and highly available communication for modern railway signalling systems while complying with the communication safety principles of DIN EN 50159.

The analysis began by introducing the overall architecture of the protocol and the communication requirements that motivate its design. The report then examined the mission-critical mechanisms implemented by RaSTA, including message integrity verification, sequence number supervision, adaptive channel monitoring, heartbeat supervision, retransmission, flow control, redundancy, and deterministic state management. These mechanisms were analysed with respect to their engineering purpose and their contribution to functional safety.

Subsequently, the report investigated the internal architecture of the protocol, including the protocol data unit, communication state machines, connection establishment procedures, and the interaction between the Safety and Retransmission Layer and the Redundancy Layer. The operational scenarios defined by DIN VDE V 0831-200 were analysed to demonstrate how the protocol behaves during both normal communication and abnormal operating conditions such as delayed messages, retransmission, communication interruption, and protocol incompatibility.

The Safety Case developed in the final technical chapter demonstrated that RaSTA addresses the communication hazards identified by DIN EN 50159 through multiple independent protection mechanisms. Rather than relying on the reliability of the underlying transport network, the protocol continuously verifies communication correctness throughout the lifetime of every connection. Whenever communication correctness can no longer be guaranteed, the protocol follows a fail-safe philosophy by terminating the communication session before unsafe information reaches the railway signalling application.

Overall, the analysis shows that RaSTA provides a comprehensive framework for safety-related railway communication. Its layered architecture, deterministic protocol behaviour, adaptive timing supervision, retransmission capabilities, and redundancy mechanisms collectively provide high communication integrity and availability while satisfying the functional safety requirements expected of modern railway signalling systems.

The report therefore concludes that the Rail Safe Transport Application protocol represents a robust and well-engineered communication solution for distributed railway control systems operating in safety-critical environments.
# 9. References

[1] DIN VDE V 0831-200:2025-06, *Railway Applications – Communication, Signalling and Processing Systems – Safe Rail Application Transmission (RaSTA)*, VDE Verlag GmbH, Berlin, Germany, June 2025.

[2] DIN EN 50159:2011, *Railway Applications – Communication, Signalling and Processing Systems – Safety-related Communication in Transmission Systems*, CENELEC.

[3] R. Rivest, *The MD4 Message-Digest Algorithm*, RFC 1320, Internet Engineering Task Force (IETF), April 1992.

[4] International Electrotechnical Commission (IEC), *IEC 61508: Functional Safety of Electrical/Electronic/Programmable Electronic Safety-related Systems*, IEC, Geneva.

[5] CENELEC, *EN 50126 Railway Applications – The Specification and Demonstration of Reliability, Availability, Maintainability and Safety (RAMS)*.

[6] CENELEC, *EN 50128 Railway Applications – Communication, Signalling and Processing Systems – Software for Railway Control and Protection Systems*.

[7] CENELEC, *EN 50129 Railway Applications – Communication, Signalling and Processing Systems – Safety-related Electronic Systems for Signalling*.
