# 2. System Overview

## 2.1 Introduction

The Rail Safe Transport Application (RaSTA) is a standardized communication protocol developed for safety-related railway signalling applications. It provides secure and highly available communication between distributed railway systems while remaining independent of the application layer. The protocol is specified in **DIN VDE V 0831-200** and is designed to satisfy the communication safety requirements defined by **DIN EN 50159**.

Unlike conventional communication protocols, RaSTA assumes that the underlying transport network cannot guarantee safe communication. Instead, the protocol itself implements mechanisms for integrity verification, sequence supervision, timeliness monitoring, retransmission, and redundancy. These mechanisms enable RaSTA to operate safely over standard transport technologies such as UDP and TCP.

---

## 2.2 Position of RaSTA within Railway Communication Systems

The RaSTA specification defines the scope of the protocol and its relationship to the surrounding communication architecture.

![Classification and Scope of RaSTA](../figures/figure-2-1-rasta-scope-and-classification.png)

**Figure 2-1.** Classification and scope of the RaSTA protocol (adapted from DIN VDE V 0831-200 [1]).

As illustrated in Figure 2-1, RaSTA is positioned between the application layer and the underlying transport services. The protocol focuses exclusively on providing safe and highly available communication and deliberately does not define any application-specific behaviour.

The application layer is therefore free to implement signalling logic, train control algorithms, or interlocking functions independently of the communication protocol. Similarly, the transport layer may consist of conventional Commercial Off-The-Shelf (COTS) technologies such as UDP or TCP without requiring safety-specific modifications.

This separation allows the same RaSTA implementation to be reused across multiple railway applications while maintaining compliance with railway communication standards.

---

## 2.3 Objectives of the RaSTA Protocol

The principal objective of RaSTA is to ensure that only valid and timely information reaches the receiving application.

To achieve this objective, the protocol implements mechanisms that detect:

* Message corruption
* Message loss
* Message duplication
* Incorrect message ordering
* Excessive communication delay
* Unauthorized communication
* Communication channel failures

Whenever one of these conditions cannot be resolved safely, the protocol terminates the communication connection rather than allowing potentially unsafe information to propagate to the application.

This behaviour follows the fail-safe philosophy required by railway signalling systems.

---

## 2.4 Layered Protocol Architecture

RaSTA adopts a modular layered architecture in which each protocol layer has clearly defined responsibilities.

![RaSTA Protocol Stack](../figures/figure-3-1-rasta-protocol-stack.png)

**Figure 2-2.** RaSTA layered protocol architecture and message frames (adapted from DIN VDE V 0831-200 [1]).

The protocol stack consists of four logical layers:

### Application Layer

The application layer contains the railway-specific control logic. RaSTA does not specify its implementation and treats application data as payload.

Typical applications include:

* Electronic interlockings
* Train control systems
* Trackside equipment
* On-board signalling systems

### Safety and Retransmission Layer

This layer provides the core safety functionality of RaSTA.

Its primary responsibilities include:

* Connection establishment
* Message integrity verification
* Sequence supervision
* Adaptive timeliness monitoring
* Heartbeat generation
* Flow control
* Retransmission of lost messages
* Safe connection termination

All communication safety decisions are performed within this layer.

### Redundancy Layer

The Redundancy Layer improves communication availability by transmitting identical protocol messages over one or more independent transport channels.

It performs:

* Duplicate elimination
* Sequence supervision
* Temporary buffering of out-of-order messages
* CRC verification
* Transport channel diagnostics

This layer enables continued communication even when individual transport channels experience failures.

### Transport Layer

The transport layer provides conventional network communication services.

RaSTA supports several transport technologies including:

* UDP
* TCP
* Other equivalent transport services

Unlike traditional communication systems, RaSTA does not rely on the transport layer to provide communication safety. Instead, the protocol independently verifies all received messages.

---

## 2.5 Communication Philosophy

RaSTA follows the principle that communication safety shall be independent of the reliability of the underlying network.

Rather than assuming reliable message delivery, the protocol continuously verifies:

* Message authenticity
* Message integrity
* Sequence correctness
* Communication timing
* Connection availability

Every received protocol data unit undergoes multiple verification steps before being accepted by the receiving application.

If any verification step fails, the protocol either initiates corrective actions, such as retransmission, or transitions to a predefined safe state by disconnecting the communication partner.

This philosophy allows RaSTA to operate safely over communication infrastructures that are not themselves safety-certified.

---

## 2.6 Relationship to Railway Functional Safety Standards

RaSTA was developed specifically to support the railway functional safety framework defined by European railway standards.

The protocol contributes to compliance with:

* **DIN EN 50159** by protecting against communication threats such as corruption, repetition, deletion, delay, insertion, masquerade, and resequencing.
* **DIN EN 50126** by supporting dependable railway communication throughout the system lifecycle.
* **DIN EN 50128** by providing a standardized communication interface for safety-related software.
* **DIN EN 50129** by supplying communication mechanisms suitable for safety-related signalling systems.

The protocol therefore represents a key component within the communication infrastructure of modern railway signalling systems.

---

## 2.7 Section Summary

RaSTA provides a standardized communication protocol specifically designed for safety-related railway applications. Through its layered architecture and independent communication safety mechanisms, the protocol enables secure and highly available communication over conventional transport networks.

The following sections examine each protocol layer in greater detail, beginning with the functional requirements that define the behaviour of the communication system.
