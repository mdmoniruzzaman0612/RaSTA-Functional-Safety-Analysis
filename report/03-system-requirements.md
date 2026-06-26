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