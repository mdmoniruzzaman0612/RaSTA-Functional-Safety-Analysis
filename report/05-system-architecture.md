# 5. System Architecture

## 5.1 Introduction

The architecture of the Rail Safe Transport Application (RaSTA) protocol has been designed to provide both functional safety and high communication availability for distributed railway signalling systems. Rather than relying on the underlying communication network to provide reliable data transmission, RaSTA incorporates its own protocol mechanisms that continuously supervise message integrity, communication timing, sequence correctness, and connection status.

A key design objective of the protocol is modularity. Each protocol layer performs a dedicated engineering function while remaining largely independent of the surrounding layers. This separation simplifies implementation, verification, maintenance, and future protocol evolution without affecting interoperability between different RaSTA implementations.

Unlike conventional communication protocols, where safety often depends on the reliability of the transport infrastructure, RaSTA assumes that the transport network may introduce communication faults at any time. Safety is therefore implemented entirely within the protocol itself.

This chapter examines the internal architecture of RaSTA, beginning with the Safety and Retransmission Layer before introducing the protocol data unit, message types, connection management, and the Redundancy Layer.

---

# 5.2 Layered Architecture of RaSTA

The RaSTA protocol is divided into two principal protocol layers defined by DIN VDE V 0831-200:

- Safety and Retransmission Layer
- Redundancy Layer

These layers operate between the signalling application and the transport network.

![RaSTA Protocol Stack](../figures/figure-2-2-protocol-layers-message-frame.png)

**Figure 5-1. Layered architecture of the RaSTA protocol (adapted from DIN VDE V 0831-200 [1]).**

The application software is responsible only for generating and processing signalling information. It does not implement communication safety itself. Instead, all application messages are forwarded to the Safety and Retransmission Layer.

The Safety and Retransmission Layer performs the majority of the protocol's safety functions. It is responsible for connection establishment, message integrity verification, sequence supervision, adaptive timing supervision, heartbeat generation, retransmission, and flow control. Every application message passes through this layer before being transmitted over the communication network.

Below this layer, the Redundancy Layer increases communication availability. If multiple transport channels are available, identical protocol messages are transmitted simultaneously across each channel. At the receiving side, the redundancy layer combines these physical channels into a single logical communication channel before forwarding the message to the Safety and Retransmission Layer.

This layered architecture ensures that communication safety and communication availability remain separate engineering concerns while working together to provide a reliable communication service.

---

# 5.3 Safety and Retransmission Layer

The Safety and Retransmission Layer represents the core of the RaSTA protocol. Nearly all safety-related communication mechanisms are implemented within this layer.

Its primary responsibilities include:

- Establishing communication connections.
- Supervising connection state.
- Detecting communication errors.
- Verifying message integrity.
- Monitoring message timing.
- Detecting missing or duplicated messages.
- Managing retransmissions.
- Supervising communication buffers.
- Safely terminating communication when required.

Unlike lower-layer transport protocols, this layer operates with knowledge of railway safety requirements. Every received protocol message is verified before it can influence the application.

An important characteristic of this layer is its deterministic behaviour. For every protocol event, the specification defines exactly one permitted response. This deterministic operation simplifies both protocol verification and railway safety certification.

The Safety and Retransmission Layer therefore provides the principal safety barrier between the signalling application and the communication network.

---

# 5.4 Protocol Data Unit

Communication between RaSTA instances occurs through Protocol Data Units (PDUs). Every protocol message follows the same general structure, allowing the receiver to process messages consistently regardless of their purpose.

The principal fields contained within a Safety and Retransmission Layer PDU are summarised in Table 5-1.

| Field | Length | Purpose |
|--------|--------|---------|
| Message Length | 2 Bytes | Total size of the protocol message. |
| Message Type | 2 Bytes | Identifies the protocol message. |
| Receiver ID | 4 Bytes | Identifies the intended receiver. |
| Sender ID | 4 Bytes | Identifies the transmitting RaSTA instance. |
| Sequence Number | 4 Bytes | Maintains message order. |
| Confirmed Sequence Number | 4 Bytes | Acknowledges previously received messages. |
| Timestamp | 4 Bytes | Records message transmission time. |
| Confirmed Timestamp | 4 Bytes | Confirms previous communication timing. |
| User Data | Variable | Contains application or protocol information. |
| Security Code | 8 or 16 Bytes | Protects message integrity. |

**Table 5-1. General structure of the Safety and Retransmission Layer protocol data unit (adapted from DIN VDE V 0831-200 [1]).**

Each field contributes to one or more communication safety functions. Rather than carrying only application data, every protocol message also contains information required to supervise communication correctness continuously throughout the lifetime of the connection.

For example, the sequence numbers allow detection of missing or duplicated messages, while the timestamps enable adaptive communication supervision. The Security Code protects the complete protocol message against accidental corruption during transmission.

This integrated message structure enables multiple safety mechanisms to operate simultaneously without requiring additional communication overhead.

---

# 5.5 Protocol Message Types

RaSTA defines a small set of specialised protocol messages, each responsible for a particular stage of communication.

| Message | Decimal Value | Purpose |
|----------|--------------:|---------|
| Connection Request | 6200 | Requests establishment of a communication connection. |
| Connection Response | 6201 | Confirms connection establishment. |
| Retransmission Request | 6212 | Requests retransmission of missing messages. |
| Retransmission Response | 6213 | Acknowledges a retransmission request. |
| Disconnect Request | 6216 | Terminates communication safely. |
| Heartbeat | 6220 | Supervises communication during idle periods. |
| Data | 6240 | Transfers application information. |
| Retransmitted Data | 6241 | Transfers previously lost application messages. |

**Table 5-2. Protocol message types defined by the RaSTA specification.**

Although the protocol defines only eight message types, these messages collectively support the complete communication lifecycle, including connection establishment, normal data exchange, communication supervision, recovery from transmission errors, and safe connection termination.

Each message contains the common protocol data unit described previously, while the payload differs according to the function being performed. This design simplifies protocol implementation because every received message follows the same general processing sequence before message-specific behaviour is applied.

---

# 5.6 Section Summary

This first part of the System Architecture chapter introduced the layered organisation of the RaSTA protocol and examined the central role of the Safety and Retransmission Layer. The common protocol data unit and the defined message types establish the foundation for all communication performed by the protocol.

The following section examines how these protocol messages are used during connection establishment, including the Connection Request, Connection Response, protocol version negotiation, and the three-step handshake that synchronises both communication partners before application data is exchanged.

# 5.7 Connection Establishment

Before any safety-related application data can be exchanged, both communication partners must establish a synchronized communication session. Unlike conventional client-server protocols, where a connection may be considered established after a single acknowledgement, RaSTA performs a structured handshake to ensure that both communication partners share identical communication parameters before entering normal operation.

The connection establishment procedure synchronizes:

- protocol version,
- sender and receiver identifiers,
- initial sequence numbers,
- receive buffer capacity,
- communication timing.

Only after successful completion of this procedure does the protocol enter the **Up** state, allowing application messages to be exchanged safely.

---

# 5.8 Connection Request

The first step of the handshake is the transmission of a **Connection Request (ConnReq)** message.

The Connection Request initializes the communication session by providing all information required by the receiving communication partner to establish a new protocol context.

The principal contents of the Connection Request are summarised in Table 5-3.

| Field | Purpose |
|--------|---------|
| Receiver Identifier | Specifies the destination RaSTA instance. |
| Sender Identifier | Identifies the transmitting RaSTA instance. |
| Initial Sequence Number | Random starting point for future sequence numbers. |
| Confirmed Sequence Number | Initial value (0). |
| Timestamp | Current transmission time. |
| Protocol Version | Requested protocol version. |
| Receive Buffer Size (Nsendmax) | Advertises receiver capacity. |
| Security Code | Protects message integrity. |

**Table 5-3. Principal fields of the Connection Request message (adapted from DIN VDE V 0831-200 [1]).**

A particularly important characteristic of the Connection Request is the use of a **random initial sequence number**.

Rather than beginning every communication session with sequence number zero, the protocol generates a random starting value. This significantly reduces the possibility of replay attacks because protocol messages belonging to previous communication sessions cannot accidentally satisfy the sequence verification rules of a newly established connection.

The Connection Request therefore performs considerably more than simply requesting communication. It establishes the initial synchronization required for all subsequent protocol processing.

---

# 5.9 Connection Response

After successfully validating the received Connection Request, the communication partner replies with a **Connection Response (ConnResp)** message.

The Connection Response confirms that the communication parameters proposed during connection establishment have been accepted.

Its principal fields are summarised in Table 5-4.

| Field | Purpose |
|--------|---------|
| Receiver Identifier | Destination communication partner. |
| Sender Identifier | Responding RaSTA instance. |
| Initial Sequence Number | Random starting sequence number of the responder. |
| Confirmed Sequence Number | Acknowledges the received Connection Request. |
| Timestamp | Current transmission time. |
| Confirmed Timestamp | Confirms previous communication timing. |
| Protocol Version | Accepted protocol version. |
| Receive Buffer Size | Receiver capacity of the responder. |
| Security Code | Integrity protection. |

**Table 5-4. Principal fields of the Connection Response message (adapted from DIN VDE V 0831-200 [1]).**

By exchanging Connection Response messages, both communication partners become aware of each other's communication capabilities before application data transmission begins.

The exchange of receive buffer capacities is particularly important because it enables subsequent flow control during normal communication.

---

# 5.10 Three-Step Handshake

The complete connection establishment procedure consists of three protocol messages.

![Successful Connection Establishment](../figures/figure-5-2-successful-connection-establishment.png)

**Figure 5-2. Successful connection establishment (adapted from DIN VDE V 0831-200 [1]).**

The communication sequence proceeds as follows:

**Step 1 – Connection Request**

The client transmits a Connection Request containing its protocol version, random sequence number, receiver identifier, sender identifier, and receive buffer size.

---

**Step 2 – Connection Response**

After validating the received request, the server replies with a Connection Response.

The response acknowledges the received Connection Request while simultaneously providing the server's own protocol parameters.

---

**Step 3 – Heartbeat**

The client completes the synchronization procedure by transmitting a Heartbeat message.

Only after receiving this message do both communication partners transition into the **Up** state.

At this point, application data may be exchanged.

---

Unlike conventional communication protocols, every stage of the handshake contributes directly to communication safety.

The Connection Request initializes communication.

The Connection Response confirms parameter compatibility.

The Heartbeat verifies that both communication partners have entered synchronized protocol states.

Only after successful completion of all three stages is communication considered safe.

---

# 5.11 Protocol Version Negotiation

During connection establishment, both communication partners exchange their supported protocol versions.

This procedure ensures that incompatible protocol implementations do not attempt to communicate.

The version negotiation procedure follows three possible outcomes:

1. **Identical protocol versions**

   Communication proceeds immediately.

2. **Server supports a newer compatible version**

   The server determines whether backward compatibility is possible.

3. **Server supports an older protocol version**

   The client decides whether communication using the older version is acceptable.

If compatibility cannot be established, the protocol immediately terminates the connection by transmitting a **Disconnect Request**.

This mechanism prevents communication between incompatible protocol implementations and ensures deterministic protocol behaviour across different software versions.

---

# 5.12 Why the Handshake is Important

The connection establishment procedure performs considerably more than simply opening a communication channel.

It establishes the complete communication context required by the protocol, including:

- synchronized sequence numbers,
- protocol compatibility,
- communication timing,
- receive buffer sizes,
- sender and receiver identification.

Without this synchronization, subsequent message verification would be impossible because neither communication partner would possess sufficient information to validate received protocol messages.

Consequently, the handshake represents the foundation upon which all later safety mechanisms operate.

---

# 5.13 Functional Safety Contribution

From a functional safety perspective, the connection establishment procedure provides several independent safety benefits.

First, the exchange of random sequence numbers prevents confusion between previous and newly established communication sessions.

Second, protocol version negotiation prevents incompatible software implementations from exchanging safety-related information.

Third, sender and receiver identifiers ensure that communication occurs only between the intended communication partners.

Finally, the Heartbeat completing the handshake confirms that both communication partners have reached identical protocol states before application data transmission begins.

These mechanisms significantly reduce the probability of unsafe communication immediately after connection establishment and provide the synchronized protocol context required for all subsequent communication.

# 5.14 Protocol State Machine

Once a communication connection has been established, RaSTA controls all protocol behaviour through a deterministic state machine. Every protocol event—whether initiated by the application, generated internally, or received from the communication partner—is processed according to predefined state transition rules.

This deterministic behaviour is essential for railway signalling applications because it guarantees that identical communication events always produce identical protocol responses. Such predictability simplifies verification, validation, interoperability, and functional safety certification.

Unlike many communication protocols that allow implementation-specific behaviour, the RaSTA specification explicitly defines the actions that shall be performed in every communication state.

---

# 5.15 Communication States

The Safety and Retransmission Layer defines six operational states.

![Safety Layer State Machine](../figures/figure-5-3-safety-layer-state-machine.png)

**Figure 5-3. State machine of the Safety and Retransmission Layer (adapted from DIN VDE V 0831-200 [1]).**

The communication states are summarised in Table 5-5.

| State | Description |
|--------|-------------|
| Closed | No communication exists between the two RaSTA instances. |
| Down | Communication has been requested and initialization is in progress. |
| Start | The connection establishment handshake is currently being executed. |
| Up | Normal communication is established. Application messages may be exchanged. |
| RetrReq | A retransmission request has been issued. |
| RetrRun | Missing messages are currently being retransmitted. |

**Table 5-5. Communication states of the Safety and Retransmission Layer.**

Each state represents a different stage of the communication lifecycle. Rather than allowing unrestricted message exchange, the protocol carefully controls which protocol messages are permitted within each state.

This strict control prevents invalid communication sequences that might otherwise compromise functional safety.

---

# 5.16 State Transitions

Communication begins in the **Closed** state.

When the application requests communication, the protocol initializes the required communication variables and proceeds to the **Down** state. The client then transmits a Connection Request and enters the **Start** state.

After successful completion of the three-step handshake described previously, both communication partners enter the **Up** state.

The **Up** state represents normal protocol operation. During this state the protocol exchanges:

- Data messages
- Heartbeat messages
- Retransmission requests
- Retransmission responses

Whenever a sequence error is detected, communication temporarily leaves the Up state and enters the retransmission procedure.

If retransmission is requested, the protocol enters the **RetrReq** state.

Once the requested data begins arriving, communication proceeds into **RetrRun**, where retransmitted messages are accepted until synchronization has been restored.

Finally, successful completion of retransmission returns communication to the Up state.

Whenever communication safety can no longer be guaranteed, the protocol immediately transitions back to the Closed state.

---

# 5.17 Deterministic Event Processing

A key characteristic of the RaSTA architecture is that every incoming protocol message is processed according to the current communication state.

For example:

- A Connection Request is accepted only during connection establishment.
- Application Data is accepted only when communication is already established.
- Retransmission Responses are processed only while retransmission is active.
- Messages received during inappropriate states are rejected.

This deterministic processing eliminates protocol ambiguity.

Consequently, two independent RaSTA implementations following the specification will always react identically to identical communication events.

This deterministic behaviour is particularly important for safety certification because protocol behaviour becomes predictable and fully verifiable.

---

# 5.18 Heartbeat Supervision

Once communication has entered the **Up** state, RaSTA continuously supervises the availability of both communication partners.

When application traffic is present, ordinary data messages provide sufficient evidence that communication remains operational.

During idle periods, however, no application messages may be available.

To prevent undetected communication failures, the protocol periodically generates **Heartbeat (HB)** messages.

Heartbeat messages contain the complete protocol control information required for communication supervision but do not contain application payload.

The transmission interval is controlled by the configurable heartbeat timer **Th**.

Whenever any protocol message is transmitted, the timer is restarted.

If no further protocol message is generated before **Th** expires, the protocol automatically transmits a Heartbeat message.

This mechanism guarantees continuous communication supervision regardless of application traffic.

---

# 5.19 Connection Monitoring

Heartbeat messages support continuous connection monitoring throughout the lifetime of the communication session.

The receiving communication partner maintains a supervision timer **Ti**.

Whenever a valid Data, Retransmitted Data, or Heartbeat message containing a newer confirmed timestamp is received, this timer is restarted.

If **Ti** expires before another valid communication message arrives, the protocol concludes that communication has failed.

Rather than continuing operation under uncertain communication conditions, the protocol immediately issues a Disconnect Request and returns to the Closed state.

This behaviour reflects the fail-safe philosophy of railway communication systems.

---

# 5.20 Benefits of the State Machine

The state machine provides several important engineering advantages.

First, communication behaviour remains deterministic throughout every stage of operation.

Second, invalid protocol sequences are detected immediately because every protocol message is evaluated within the context of the current communication state.

Third, retransmission is fully integrated into normal communication without requiring separate protocol logic.

Finally, safe termination of communication is guaranteed whenever protocol correctness can no longer be verified.

Instead of attempting to continue communication under uncertain conditions, the protocol intentionally disconnects, ensuring that potentially unsafe information never reaches the application layer.

---

# 5.21 Functional Safety Contribution

The protocol state machine forms the behavioural foundation of the Safety and Retransmission Layer.

Rather than allowing arbitrary communication sequences, the state machine defines precisely how every communication event shall be processed.

This deterministic behaviour contributes directly to functional safety by ensuring that:

- communication begins only after successful synchronization;
- application data is exchanged only while communication remains valid;
- retransmission occurs only under controlled conditions;
- invalid protocol events are rejected;
- communication terminates safely whenever correctness cannot be guaranteed.

Together with the protocol data unit structure discussed previously, the state machine provides the operational framework upon which all remaining RaSTA safety mechanisms are built.

# 5.22 Sequence Number Supervision

After a communication connection has been successfully established, RaSTA continuously verifies that every message is received exactly once and in the correct order. This supervision is achieved using **Sequence Numbers (SN)**, which are attached to every protocol data unit generated by the Safety and Retransmission Layer.

Each transmitted message receives a unique sequence number that is incremented after every successful transmission. The receiver maintains its own expected sequence number and compares it with the sequence number contained in every received protocol data unit.

If the received sequence number matches the expected value, the message is accepted for further processing. Otherwise, the protocol assumes that communication has been disturbed and initiates the retransmission procedure.

This mechanism enables the protocol to detect:

- Lost messages
- Duplicate messages
- Replay attacks
- Messages arriving out of sequence
- Unexpected message insertion

Unlike conventional transport protocols that primarily aim to guarantee reliable delivery, RaSTA performs sequence supervision as a functional safety mechanism. The objective is not simply to recover missing information, but to ensure that no unsafe communication reaches the signalling application.

---

# 5.23 Confirmed Sequence Numbers

In addition to ordinary sequence numbers, RaSTA maintains **Confirmed Sequence Numbers (CS)**.

While the Sequence Number identifies the current protocol message, the Confirmed Sequence Number acknowledges previously received messages.

Three internal variables are used:

| Variable | Purpose |
|-----------|---------|
| **CST** | Sequence number acknowledged in the next transmitted message. |
| **CSR** | Most recently confirmed sequence number received from the communication partner. |
| **CSPDU** | Confirmed sequence number contained within the received protocol message. |

**Table 5-6. Confirmed sequence number variables used by RaSTA.**

Whenever a valid message is received, the receiver stores its sequence number and later includes this value in the next outgoing message. This implicit acknowledgement eliminates the need for dedicated acknowledgement packets and reduces protocol overhead.

The sender continuously evaluates received acknowledgements. Once a transmitted message has been confirmed, it is removed from the retransmission buffer, ensuring that only outstanding messages remain available for possible recovery.

---

# 5.24 Flow Control

Reliable communication also requires that neither communication partner transmits messages faster than the other can process them.

For this purpose, RaSTA implements a flow control mechanism based on the configuration parameter **Nsendmax**.

During connection establishment, both communication partners exchange the maximum number of outstanding messages they are able to buffer.

The sender then monitors the number of transmitted but unacknowledged messages.

If this number reaches the receiver's advertised limit, additional transmissions are temporarily delayed until acknowledgements become available.

This mechanism prevents receiver buffer overflow while allowing communication to continue efficiently under normal operating conditions.

Unlike many conventional flow control algorithms that focus primarily on network efficiency, RaSTA uses flow control to preserve deterministic protocol behaviour and prevent communication failures caused by excessive message generation.

---

# 5.25 Packetization

Applications frequently generate multiple small messages within a short period of time.

Sending every application message individually would increase protocol overhead and reduce communication efficiency.

To address this issue, RaSTA supports **packetization**, allowing several application messages to be combined into a single protocol data unit.

Each application message is preceded by its own length field, enabling the receiver to reconstruct the original message boundaries after reception.

The maximum number of application messages that may be combined into a single protocol message is determined by the configuration parameter **NmaxPackage**.

Although packetization improves communication efficiency, it remains completely transparent to the application software. After reception, the Safety and Retransmission Layer separates the individual application messages before forwarding them to the signalling application.

Consequently, packetization improves bandwidth utilisation without changing application behaviour.

---

# 5.26 Retransmission Mechanism

Despite highly reliable communication networks, occasional packet loss remains possible.

Rather than immediately terminating communication after detecting a missing message, RaSTA first attempts to recover the missing information using its retransmission mechanism.

The retransmission procedure begins when the receiver detects an unexpected sequence number.

Instead of forwarding the message to the application, the protocol temporarily suspends normal communication and sends a **Retransmission Request (RetrReq)** to the communication partner.

The request identifies the last correctly received sequence number, allowing the sender to determine exactly which messages must be retransmitted.

The sender then performs the following operations:

1. Searches the retransmission buffer.
2. Sends a Retransmission Response.
3. Retransmits all missing application messages.
4. Completes retransmission using either a Heartbeat or a normal Data message.

Throughout this process, application data remains protected from incomplete communication because messages are released only after the correct sequence has been restored.

---

# 5.27 Retransmission States

The retransmission mechanism introduces two dedicated communication states into the Safety and Retransmission Layer.

| State | Description |
|--------|-------------|
| **RetrReq** | Waiting for retransmission response after detecting a sequence error. |
| **RetrRun** | Retransmitted messages are currently being received and processed. |

**Table 5-7. Retransmission states of the Safety and Retransmission Layer.**

These states temporarily suspend ordinary application communication while protocol synchronization is restored.

Once all missing messages have been received successfully, communication automatically returns to the normal **Up** state.

If synchronization cannot be restored before the adaptive supervision timer expires, the protocol safely terminates the communication session.

This behaviour demonstrates the balance between communication availability and functional safety that characterises the RaSTA protocol.

---

# 5.28 Engineering Perspective

From an engineering viewpoint, sequence supervision, acknowledgements, flow control, and retransmission represent complementary mechanisms rather than independent protocol functions.

Sequence supervision detects communication faults.

Confirmed sequence numbers acknowledge successful reception.

Flow control prevents communication overload.

Retransmission restores missing information.

Together these mechanisms maintain communication consistency throughout the entire communication session while ensuring that incomplete or inconsistent information is never delivered to the application.

Instead of assuming perfect communication, RaSTA continuously validates every stage of message exchange and performs corrective actions whenever communication integrity is threatened.

This layered approach significantly improves both communication reliability and functional safety compared with conventional transport protocols that rely primarily on the underlying network infrastructure.

# 5.29 Redundancy Layer

While the Safety and Retransmission Layer ensures communication correctness, the **Redundancy Layer** is responsible for improving communication availability.

Safety and availability are closely related but represent different engineering objectives. Safety ensures that incorrect information is never accepted, whereas availability ensures that correct information continues to be delivered even when communication faults occur.

To improve availability, RaSTA allows a single logical communication channel to be implemented using multiple independent physical transport channels.

Instead of relying on one communication path, identical protocol messages are transmitted simultaneously across every configured transport channel.

If one transport channel becomes delayed, corrupted, or unavailable, another transport channel can still successfully deliver the message.

This design significantly improves communication availability without changing the behaviour observed by the Safety and Retransmission Layer.

---

# 5.30 Redundancy Channel Model

The operation of the Redundancy Layer is illustrated by the channel model defined in the DIN specification.

![Redundancy Channel Model](../figures/figure-6-1-redundancy-channel-model.png)

**Figure 5-4. Channel model of the Redundancy Layer (adapted from DIN VDE V 0831-200 [1]).**

As shown in Figure 5-4, a single logical redundancy channel is formed from one or more independent transport channels.

During transmission, every outgoing protocol message is duplicated and transmitted simultaneously across all configured transport channels.

At the receiving side, the Redundancy Layer compares the received messages and forwards only one valid copy to the Safety and Retransmission Layer.

Duplicate messages arriving from slower transport channels are discarded after diagnostic evaluation.

Consequently, the upper protocol layers remain completely unaware that multiple transport channels are being used.

This abstraction greatly simplifies protocol implementation because all higher protocol layers operate exactly as if only one communication channel existed.

---

# 5.31 Sequence Supervision in the Redundancy Layer

Unlike the Safety and Retransmission Layer, which supervises application-level communication, the Redundancy Layer maintains its own sequence numbers for every redundancy channel.

Each transmitted redundancy message receives an incrementing sequence number.

The receiver compares the received sequence number with the next expected value.

Three situations are possible:

- **Expected sequence number received**  
  The message is immediately forwarded to the Safety and Retransmission Layer.

- **Future sequence number received**  
  The message is temporarily stored inside the **Defer Queue** while the protocol waits for the missing message.

- **Old or duplicate sequence number received**  
  The message is discarded because it has already been processed previously.

This mechanism ensures that temporary differences in transport channel latency do not unnecessarily trigger retransmission procedures within the upper protocol layers.

---

# 5.32 Defer Queue

One of the key mechanisms implemented by the Redundancy Layer is the **Defer Queue**.

The Defer Queue temporarily stores messages that arrive earlier than expected.

For example, consider two transport channels:

- Transport Channel A
- Transport Channel B

If message **101** is delayed on Channel A but message **102** arrives first through Channel B, the receiver cannot immediately forward message 102 because message 101 has not yet been delivered.

Instead, message 102 is placed inside the Defer Queue.

The protocol then waits for a configurable period **Tseq**.

If message 101 arrives before this timer expires:

- message 101 is forwarded first,
- followed immediately by message 102.

The original message order is therefore preserved.

If the timer expires before message 101 arrives, the queued message is released to the Safety and Retransmission Layer.

This mechanism prevents unnecessary communication delays while preserving the correct ordering of messages whenever possible.

---

# 5.33 Redundancy State Machine

Compared with the Safety and Retransmission Layer, the Redundancy Layer uses a much simpler state machine.

![Redundancy Layer State Machine](../figures/figure-6-2-redundancy-state-machine.png)

**Figure 5-5. State transitions of the Redundancy Layer (adapted from DIN VDE V 0831-200 [1]).**

Only two communication states are defined.

| State | Description |
|---------|-------------|
| **Closed** | No redundancy channel exists. |
| **Up** | The redundancy channel is operational. |

**Table 5-8. Communication states of the Redundancy Layer.**

When the upper protocol layer requests communication, the Redundancy Layer initializes all internal variables and enters the **Up** state.

When communication is terminated, all queues, timers, and sequence numbers are released before returning to the **Closed** state.

The simplicity of this state machine reflects the fact that the Redundancy Layer is responsible only for transport availability rather than communication safety.

---

# 5.34 Principal Redundancy Functions

DIN VDE V 0831-200 defines several internal functions that implement the behaviour of the Redundancy Layer.

The most important functions are summarised in Table 5-9.

| Function | Purpose |
|----------|---------|
| **f_init()** | Initializes a new redundancy channel. |
| **f_cleanup()** | Releases all redundancy resources. |
| **f_receiveData()** | Verifies and processes received transport messages. |
| **f_sendData()** | Sends identical messages through every transport channel. |
| **f_deferTmo()** | Processes messages remaining inside the Defer Queue after timeout. |
| **f_deliverDeferQueue()** | Delivers queued messages in the correct sequence. |

**Table 5-9. Principal functions implemented by the Redundancy Layer.**

Although these functions operate internally, together they implement the complete redundancy behaviour defined by the specification.

Their responsibilities remain clearly separated from those of the Safety and Retransmission Layer, preserving the modular architecture of the protocol.

---

# 5.35 Contribution to Functional Safety

The Redundancy Layer contributes primarily to **communication availability** rather than communication integrity.

Integrity protection remains the responsibility of the Safety and Retransmission Layer.

Nevertheless, redundancy indirectly improves functional safety because uninterrupted communication reduces the probability that signalling applications lose communication with remote safety devices.

By masking temporary failures affecting individual transport channels, the Redundancy Layer allows communication to continue without triggering unnecessary protocol disconnects.

At the same time, delayed or duplicate transport messages are carefully supervised before they are forwarded to higher protocol layers, ensuring that increased availability does not compromise communication correctness.

The clear separation between availability mechanisms and safety mechanisms represents one of the strongest architectural characteristics of the RaSTA protocol.

---

# 5.36 Chapter Summary

This chapter examined the internal architecture of the Rail Safe Transport Application protocol. The layered organisation of the protocol separates communication safety from communication availability, allowing each protocol layer to perform a dedicated engineering function.

The chapter introduced the Safety and Retransmission Layer, the protocol data unit structure, protocol message types, connection establishment procedure, state machine, heartbeat supervision, sequence monitoring, confirmed sequence numbers, flow control, packetization, retransmission, and finally the Redundancy Layer.

Together, these architectural elements form a deterministic communication framework capable of detecting, correcting, and supervising communication faults before they influence railway signalling applications. Every protocol component contributes to the layered defence strategy required for safety-related railway communication.

Having established the protocol architecture, the following chapter evaluates the **dynamic behaviour** of RaSTA by analysing the operational scenarios defined in DIN VDE V 0831-200. These scenarios demonstrate how the protocol behaves during normal communication, retransmission, communication failures, and recovery procedures, providing practical evidence of the protocol's correct functional operation.