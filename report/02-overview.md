# 2. Overview

## 2.1 Background

Safety-related railway signalling systems depend on reliable communication between distributed electronic and programmable electronic subsystems. If communication failures are not detected and controlled, incorrect or outdated information may influence signalling functions and create hazardous operating conditions.

RaSTA, the Rail Safe Transport Application, is specified in DIN VDE V 0831-200 as a generic protocol stack for secure and highly available communication in railway signalling systems [1]. The protocol provides mechanisms for safe data transmission in accordance with DIN EN 50159 and supports operation over networks of category 1 and category 2 [1].

The protocol is intentionally independent of the application layer. This means that RaSTA does not define railway application logic itself. Instead, it provides a safety-related communication service that can be used by different railway signalling applications [1].

---

## 2.2 Purpose of RaSTA

The purpose of RaSTA is to provide:

* Safe communication according to DIN EN 50159
* High availability through redundant transport channels
* Retransmission of lost or corrupted messages
* Timeliness monitoring of application messages
* Connection supervision using heartbeat messages
* Diagnostic information for communication monitoring

The protocol does not prescribe a concrete software implementation. Instead, it specifies protocol data units, message formats, state transitions, and abstract protocol functions that must be implemented for compatibility [1].

---

## 2.3 Protocol Stack

RaSTA consists of two main protocol layers:

1. **Safety and Retransmission Layer**
2. **Redundancy Layer**

The Safety and Retransmission Layer provides the safety-related communication mechanisms. These include sequence supervision, confirmed sequence numbers, timestamp-based timeliness monitoring, security code verification, connection establishment, heartbeat supervision, and retransmission of lost data [1].

The Redundancy Layer improves availability by transmitting the same information over one or more transport channels. If one transport channel loses or corrupts a message, the redundant channel may still provide a valid copy of the same message [1].

The underlying transport layer may use standard communication services such as UDP or TCP. A connectionless transport service is sufficient because RaSTA performs its own connection supervision [1].

---

## 2.4 Scope and Limitations

RaSTA is intended for safety-related electrical, electronic, and programmable electronic systems used in railway signalling applications [1].

The scope includes:

* Message formats
* Protocol data units
* State transitions
* Connection establishment
* Retransmission handling
* Redundancy handling
* Timing supervision
* Diagnostic functions

The scope does not include:

* Application-layer behaviour
* Specific railway application protocols
* Concrete software interfaces
* Concrete transport-layer APIs
* Cybersecurity protection against unauthorized access in open networks

The RaSTA standard explicitly states that communication over open networks vulnerable to unauthorized access is not considered. If such threats are relevant, additional security measures must be implemented separately [1].

---

## 2.5 Interfaces

RaSTA provides an interface to the application layer and an interface to the transport layer.

The application interface includes services such as:

* OpenConnection()
* CloseConnection()
* SendData()
* ReceiveData()
* ConnectionStateRequest()

It also provides notifications for received messages, connection state changes, and diagnostic information [1].

The transport interface provides a transparent communication service for sending and receiving protocol data units. It maps RaSTA messages to transport addresses such as IP addresses, port numbers, and protocol types [1].

---

## 2.6 RaSTA Network

A RaSTA network is defined by two conditions:

1. The involved RaSTA instances can communicate at transport-layer level.
2. The instances use a common safety code at the Safety and Retransmission Layer [1].

Sender and receiver identifiers are unique only within a specific RaSTA network. Therefore, the RaSTA network identifier and the initial value used for the MD4 safety-code calculation must have a unique mapping [1].

This concept allows one physical communication infrastructure to be divided into separate RaSTA networks by using different safety-code initial values.

---

## 2.7 Relevance to Functional Safety

RaSTA is relevant to functional safety because it implements mechanisms that detect and control communication failures before they can influence safety-related railway applications.

The main safety-relevant mechanisms are:

* Security code verification
* Sender and receiver identification
* Sequence number supervision
* Confirmed sequence number supervision
* Timestamp and confirmed timestamp monitoring
* Heartbeat-based connection supervision
* Retransmission of lost data
* Safe disconnection when communication validity cannot be guaranteed

These mechanisms form the technical basis for the functional safety analysis developed in the following sections.
