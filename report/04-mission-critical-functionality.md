# 4. Mission Critical Functionality

## 4.1 Introduction

The primary objective of the Rail Safe Transport Application (RaSTA) protocol is to ensure that safety-critical information exchanged between railway signalling systems remains correct, complete, timely, and available throughout the entire communication process. Unlike conventional communication protocols that depend on the reliability of the underlying transport network, RaSTA assumes that communication channels may introduce transmission errors, delays, message losses, duplication, or incorrect sequencing. Consequently, the protocol implements its own comprehensive collection of safety mechanisms to detect these communication faults before they can influence the behaviour of safety-related railway applications.

This design philosophy follows the requirements defined in **DIN EN 50159**, which specifies the protection mechanisms required for safety-related communication over transmission systems. Instead of assuming that the communication network is trustworthy, RaSTA continuously verifies every received message using multiple independent protection mechanisms. Only after all verification procedures have been successfully completed is the received information forwarded to the application layer.

The protocol therefore follows the **fail-safe principle**, meaning that whenever communication correctness cannot be guaranteed, the communication channel is intentionally closed rather than risking the delivery of potentially unsafe information. This deterministic behaviour is one of the key characteristics that make RaSTA suitable for railway signalling applications with high Safety Integrity Level (SIL) requirements.

---

## 4.2 Mission-Critical Functions of RaSTA

The RaSTA specification defines several protocol mechanisms that operate simultaneously throughout the lifetime of every communication connection. Each mechanism addresses one or more communication threats identified by DIN EN 50159. Rather than relying on a single protection method, RaSTA employs multiple independent safety barriers that together create a robust communication architecture.

The principal mission-critical functions implemented by the protocol are summarised in Table 4-1.

| Mission-Critical Function | Purpose |
|--------------------------|---------|
| Safe Connection Establishment | Ensures both communication partners are synchronised before user data is exchanged. |
| Message Integrity Verification | Detects accidental corruption of transmitted messages using an MD4-based safety code. |
| Sender and Receiver Authentication | Confirms that messages originate from the expected communication partner. |
| Sequence Number Supervision | Detects message loss, duplication, replay, insertion, and incorrect ordering. |
| Adaptive Time Monitoring | Ensures that delayed messages cannot compromise safe communication. |
| Heartbeat Supervision | Continuously verifies the operational availability of both communication partners. |
| Retransmission Procedure | Recovers lost messages without compromising communication safety. |
| Flow Control | Prevents receiver buffer overflow and maintains stable communication throughput. |
| Redundant Communication | Maintains communication availability using multiple transport channels. |
| Safe Disconnection | Terminates communication whenever protocol correctness cannot be guaranteed. |

**Table 4-1. Mission-critical functions implemented by the RaSTA protocol.**

Each of these functions contributes independently to the overall functional safety argument. The failure of one mechanism does not immediately compromise communication safety because the remaining mechanisms continue to supervise the communication process. This layered approach follows one of the fundamental engineering principles used throughout railway signalling systems.

---

## 4.3 Protocol Interfaces

The protocol architecture separates communication safety from application functionality through well-defined interfaces. This modular design allows RaSTA to remain independent of the specific signalling application while providing a standardised communication service.

![RaSTA Protocol Interfaces](../figures/figure-3-2-rasta-protocol-interfaces.png)

**Figure 4-1. Interfaces within the RaSTA protocol stack (adapted from DIN VDE V 0831-200 [1]).**

Figure 4-1 illustrates the interfaces between the application layer, the Safety and Retransmission Layer, the Redundancy Layer, and the underlying transport adaptation. The interfaces shown in yellow represent compatibility-relevant components defined explicitly by the standard. These interfaces include message formats, protocol behaviour, state transitions, and protocol processing rules. Every compliant RaSTA implementation must follow these requirements exactly to ensure interoperability between different implementations.

The white interfaces are classified as non-compatibility-relevant. These include the application programming interface and the transport adaptation interface. Their implementation may differ between platforms provided that the externally visible protocol behaviour remains identical.

This separation provides several engineering advantages. The signalling application can be developed independently from the communication protocol, allowing the same RaSTA implementation to be reused in different railway applications. Similarly, different operating systems or network technologies may implement different transport adaptations without affecting protocol compatibility.

---

## 4.4 Communication Services

The Safety and Retransmission Layer provides several communication services to the application layer. These services define the complete lifecycle of a communication connection.

The most important services include:

- **OpenConnection()** initializes communication between two RaSTA instances by providing the sender identifier, receiver identifier, and RaSTA network identifier. Before a new connection is established, both communication partners should remain in the Closed state for at least the maximum communication timeout (**Tmax**) to ensure that no obsolete protocol information remains active.

- **CloseConnection()** safely terminates an existing communication connection. Depending on the reason for termination, additional diagnostic information may be transmitted to assist system maintenance and fault analysis.

- **SendData()** transfers application messages through the Safety and Retransmission Layer. Before transmission, the protocol automatically assigns sequence numbers, timestamps, confirmation information, and an integrity protection code.

- **ReceiveData()** delivers validated application messages to the receiving application. Messages are delivered only after all protocol verification procedures have completed successfully.

- **ConnectionStateRequest()** allows the application to monitor the current communication state, local buffer utilisation, and the available receive buffer of the remote communication partner.

These services provide a clear separation between the application software and the communication protocol. The application therefore remains unaware of protocol-specific details such as retransmissions, heartbeat generation, sequence supervision, or communication timing.

---

## 4.5 Communication Notifications

Besides communication services, RaSTA continuously informs the application layer about important protocol events through notification mechanisms.

Three categories of notifications are defined by the specification:

**Receive Notification**

Whenever a valid application message becomes available, the protocol informs the application that new data is ready for processing. This notification occurs only after all protocol safety checks have been completed successfully.

**Connection State Notification**

Whenever the protocol changes its communication state—for example, during connection establishment, retransmission, or disconnection—the application receives an updated status notification. This allows higher-level railway applications to react immediately to communication changes.

**Diagnostic Notification**

The protocol also generates diagnostic information during operation. These reports include measured communication delays, heartbeat timing statistics, retransmission behaviour, transport-channel quality, and protocol error counters. Such information is particularly valuable during commissioning, maintenance, and fault investigation, allowing engineers to evaluate communication performance without affecting the safety behaviour of the protocol itself.

Together, these services and notifications create a comprehensive communication interface while maintaining complete separation between application logic and protocol implementation.

## 4.6 Adaptive Channel Monitoring

One of the distinguishing characteristics of the RaSTA protocol is its adaptive approach to communication supervision. Traditional communication protocols often rely on fixed timeout values to determine whether a communication channel has failed. Such an approach is unsuitable for railway signalling because communication delays vary depending on network load, routing, processing time, and transmission medium. A timeout that is too short may disconnect a healthy communication channel, while a timeout that is too long may delay the detection of dangerous communication failures.

To overcome this limitation, RaSTA continuously measures the behaviour of the communication channel and dynamically adjusts its supervision timers according to the observed network conditions. This mechanism is referred to as **Adaptive Channel Monitoring**.

![Adaptive Channel Monitoring](../figures/figure-4-1-adaptive-channel-monitoring.png)

**Figure 4-2. Adaptive channel monitoring used by RaSTA to supervise communication timing (adapted from DIN VDE V 0831-200 [1]).**

Figure 4-2 illustrates how the protocol continuously evaluates communication timing during normal operation. Instead of relying on predetermined timeout values, each received protocol message contributes to a new estimation of the communication behaviour between the sender and the receiver.

The adaptive supervision algorithm is based on several timing parameters defined by the RaSTA specification.

### Round Trip Delay (Trtd)

The **Round Trip Delay (Trtd)** represents the measured time required for a message to travel from one communication partner to the other and for the corresponding confirmation to return.

Unlike theoretical network latency, Trtd includes all delays introduced by:

- Message transmission
- Network routing
- Transport protocol processing
- Protocol stack processing
- Application processing
- Confirmation transmission

Because Trtd is continuously measured during normal communication, the protocol always maintains an up-to-date estimate of the actual communication performance.

---

### Alive Delay (Talive)

The second measured parameter is the **Alive Delay (Talive)**.

Talive represents the maximum expected delay between two consecutive time-monitoring-relevant protocol messages. These messages include:

- Data messages
- Retransmitted Data messages
- Heartbeat messages

Whenever no application data needs to be transmitted, heartbeat messages ensure that Talive can still be measured.

Consequently, the communication channel remains under continuous supervision even during long periods without user traffic.

---

### Maximum Acceptable Message Age (Tmax)

The maximum acceptable message age, denoted as **Tmax**, is configured during system design.

Tmax specifies the oldest message that may still be considered valid by the receiving application.

If a communication delay exceeds this limit, the transmitted information may no longer represent the current state of the railway system. Using such outdated information could potentially result in unsafe control decisions.

Therefore, Tmax represents a functional safety requirement rather than merely a communication performance parameter.

---

### Adaptive Supervision Timer (Ti)

Using the measured communication delays together with Tmax, RaSTA continuously calculates a supervision timer known as **Ti**.

Rather than remaining fixed, Ti is recalculated whenever new communication timing information becomes available.

This adaptive behaviour enables the protocol to tolerate temporary communication fluctuations while still detecting abnormal delays quickly.

Whenever a protocol message containing a newer **Confirmed Timestamp (CTS)** is received, the receiver immediately restarts the supervision timer.

If the timer expires before another valid time-monitoring-relevant message arrives, the communication partner is considered unavailable.

The protocol then initiates a safe disconnection procedure.

---

## 4.7 Timestamp-Based Communication Supervision

Every protocol data unit transmitted by the Safety and Retransmission Layer contains two timestamp fields:

| Timestamp Field | Purpose |
|-----------------|---------|
| Timestamp (TS) | Records the local transmission time of the current message. |
| Confirmed Timestamp (CTS) | Confirms the timestamp of the previously received message. |

**Table 4-2. Timestamp fields used for adaptive communication monitoring.**

Unlike sequence numbers, timestamps are not primarily intended to detect message loss. Instead, they allow the protocol to estimate the current communication performance.

Whenever a message arrives, the receiver compares the received Confirmed Timestamp (CTS) with the locally stored value.

If the received CTS is newer than the previously confirmed timestamp, the message is considered suitable for updating the adaptive supervision timers.

Messages containing obsolete timestamps are rejected because they may indicate delayed or replayed communication.

---

## 4.8 Why Adaptive Monitoring Improves Functional Safety

The adaptive monitoring mechanism provides several important safety advantages over conventional fixed timeout systems.

First, it prevents unnecessary communication failures during periods of increased network latency. Since the timeout values are derived from actual communication measurements, temporary increases in transmission delay do not immediately cause unnecessary connection terminations.

Second, adaptive supervision enables rapid detection of genuine communication failures. If communication suddenly stops or messages become excessively delayed, the calculated supervision timer expires quickly, allowing the protocol to transition to a predefined safe state.

Third, the mechanism ensures that outdated information cannot remain undetected within the communication channel. Messages arriving after the maximum acceptable communication delay are no longer considered valid for safety-related decision making.

Finally, adaptive monitoring provides deterministic behaviour that can be analysed during the railway safety certification process. Every timeout calculation follows the same algorithm, ensuring predictable protocol behaviour under both normal and abnormal operating conditions.

---

## 4.9 Relationship to DIN EN 50159

DIN EN 50159 identifies excessive communication delay as one of the principal threats affecting safety-related transmission systems.

Adaptive channel monitoring directly addresses this threat by continuously evaluating communication timing throughout the entire lifetime of the connection.

Instead of assuming that every received message remains valid indefinitely, RaSTA verifies that every message satisfies the timing constraints defined by the railway application.

This mechanism therefore protects against:

- Delayed messages
- Communication interruption
- Obsolete information
- Undetected communication failures

Together with sequence supervision and message integrity verification, adaptive channel monitoring forms one of the three principal safety barriers implemented by the RaSTA protocol.

## 4.10 Message Integrity Verification

One of the fundamental objectives of the RaSTA protocol is to ensure that every message delivered to the receiving application is identical to the message transmitted by the sender. During transmission, communication channels may introduce bit errors, corrupted packets, incomplete frames, or accidental modifications. Even a single altered bit may completely change the meaning of a safety-critical command.

To prevent corrupted information from reaching the application, every protocol data unit transmitted by the Safety and Retransmission Layer contains a **Security Code**. This security code is calculated over the entire protocol message before transmission and verified immediately after reception.

Unlike ordinary communication protocols that often rely solely on lower-layer error detection, RaSTA performs its own integrity verification independently of the transport network. Consequently, communication safety does not depend on the reliability of Ethernet, UDP, TCP, switches, routers, or any other Commercial Off-The-Shelf (COTS) networking equipment.

---

## 4.11 Structure of the Protocol Data Unit

Every message exchanged by the Safety and Retransmission Layer follows a common Protocol Data Unit (PDU) format defined by DIN VDE V 0831-200.

The PDU consists of several fields, each serving a specific purpose in ensuring safe communication.

| Field | Purpose |
|--------|---------|
| Message Length | Specifies the total size of the protocol message. |
| Message Type | Identifies the protocol function being performed. |
| Receiver ID | Identifies the intended communication partner. |
| Sender ID | Identifies the transmitting RaSTA instance. |
| Sequence Number | Maintains message order and detects missing messages. |
| Confirmed Sequence Number | Acknowledges previously received messages. |
| Timestamp | Indicates the transmission time of the message. |
| Confirmed Timestamp | Confirms the timestamp of previously received data. |
| User Data | Contains application or protocol-specific information. |
| Security Code | Protects message integrity. |

**Table 4-3. Principal fields of the Safety and Retransmission Layer Protocol Data Unit.**

Each field contributes to one or more communication safety mechanisms. Rather than relying on a single integrity check, RaSTA combines several independent protection mechanisms inside every protocol message.

---

## 4.12 Security Code

The Security Code is calculated after all protocol fields have been completed.

The calculation includes:

- Message Length
- Message Type
- Receiver Identifier
- Sender Identifier
- Sequence Numbers
- Timestamps
- User Data

The resulting integrity value is appended to the end of the protocol data unit before transmission.

When the receiver obtains the message, it performs exactly the same calculation.

If the calculated value differs from the transmitted Security Code, the message is immediately discarded.

The protocol then increments the corresponding error counter and continues waiting for the next valid protocol message.

At no point is a corrupted message delivered to the application.

---

## 4.13 MD4 Integrity Mechanism

DIN VDE V 0831-200 specifies three possible Security Code configurations.

| Option | Description |
|----------|-------------|
| Option 1 | No Security Code (SIL0 applications only) |
| Option 2 | Lower 64 bits of the MD4 Message Digest |
| Option 3 | Complete 128-bit MD4 Message Digest |

**Table 4-4. Security Code options defined by the RaSTA specification.**

For safety-related railway signalling systems, the protocol normally uses either the lower half or the complete MD4 message digest.

The specification also allows the MD4 algorithm to use **project-specific initial values** instead of the standard RFC 1320 initialization constants.

Using different initial values allows independent RaSTA networks to coexist within the same physical communication infrastructure while preventing accidental interaction between unrelated systems.

This mechanism effectively creates logically independent communication domains.

---

## 4.14 Sender and Receiver Identification

Every protocol message contains both a **Sender Identifier** and a **Receiver Identifier**.

These identifiers uniquely specify the two communication partners participating in a RaSTA connection.

Upon receiving a message, the protocol verifies that:

- the Sender Identifier matches the expected communication partner;
- the Receiver Identifier corresponds to the local RaSTA instance.

Messages failing either verification are rejected immediately.

This mechanism prevents messages intended for another communication partner from being processed accidentally.

Combined with the project-specific MD4 initialization values, sender and receiver identifiers ensure that communication remains isolated within the intended RaSTA network.

---

## 4.15 Sequence Number Supervision

While message integrity verification detects corruption, it cannot detect whether messages have been lost, duplicated, or received in the wrong order.

To address these threats, RaSTA assigns every transmitted protocol message a **Sequence Number**.

Internally, the protocol maintains several sequence-related variables.

| Variable | Description |
|-----------|-------------|
| **SNT** | Next sequence number to be transmitted. |
| **SNR** | Next sequence number expected by the receiver. |
| **SNPDU** | Sequence number contained in the received protocol data unit. |

**Table 4-5. Sequence number variables used by RaSTA.**

Whenever a new protocol message is transmitted, the sender copies the current value of **SNT** into the protocol data unit before incrementing the transmit counter.

The receiver compares the received **SNPDU** with its locally expected value **SNR**.

Only messages arriving with the correct sequence number are accepted immediately.

---

## 4.16 Sequence Number Range Check

The sequence verification procedure actually consists of two independent stages.

The first stage performs a **Sequence Number Range Check**.

This verification determines whether the received sequence number lies within a reasonable distance from the expected value.

Messages whose sequence numbers differ excessively from the expected sequence number are considered implausible and are discarded immediately.

This prevents corrupted messages containing unrealistic sequence numbers from unnecessarily triggering retransmission procedures.

Only messages passing this plausibility check proceed to the second stage.

---

## 4.17 Exact Sequence Number Verification

After the range check, the protocol performs an exact sequence comparison.

The receiver verifies whether:

```
SNPDU == SNR
```

If the comparison succeeds:

- the message is accepted,
- the receive sequence number is incremented,
- the application eventually receives the payload.

If the comparison fails:

- the message is temporarily rejected,
- a retransmission procedure is initiated,
- communication enters the RetrReq state.

This mechanism detects:

- missing messages,
- duplicated messages,
- replay attacks,
- inserted messages,
- incorrect message ordering.

Because every protocol message must satisfy this verification before reaching the application, sequence supervision forms one of the most important safety mechanisms implemented by RaSTA.

---

## 4.18 Functional Safety Contribution

Message integrity verification and sequence supervision operate together to protect against multiple communication threats identified by DIN EN 50159.

The Security Code ensures that received messages remain unmodified during transmission, while sequence supervision guarantees that messages arrive exactly once and in the correct order.

These mechanisms therefore protect against:

- corruption,
- duplication,
- omission,
- replay,
- insertion,
- resequencing.

Together they establish two independent safety barriers that significantly reduce the probability of unsafe communication behaviour reaching railway signalling applications.

## 4.19 Confirmed Sequence Numbers

While ordinary sequence numbers supervise the order of received messages, RaSTA also maintains a second sequence mechanism known as the **Confirmed Sequence Number (CS)**. This mechanism allows both communication partners to acknowledge previously received messages without requiring separate acknowledgement packets.

Internally, the protocol maintains three confirmed sequence variables.

| Variable | Description |
|-----------|-------------|
| **CST** | Sequence number that will be acknowledged in the next transmitted message. |
| **CSR** | Last confirmed sequence number received from the communication partner. |
| **CSPDU** | Confirmed sequence number contained in the received protocol data unit. |

**Table 4-6. Confirmed sequence number variables used by RaSTA.**

Whenever a protocol message is successfully received, the receiver stores its sequence number and includes this value as **CST** in the next outgoing message. This process acknowledges successful reception without introducing additional acknowledgement packets, thereby reducing communication overhead.

The sender continuously compares the received **CSPDU** with its own transmission history. Once a transmitted message has been acknowledged, it is removed from the retransmission buffer, reducing memory consumption and ensuring that only unconfirmed messages remain available for possible retransmission.

---

## 4.20 Heartbeat Supervision

Safety-related communication must remain supervised even when no application data is being exchanged. Without additional supervision, a failed communication channel could remain undetected for an extended period, potentially leaving the signalling application unaware of the failure.

To prevent this situation, RaSTA periodically transmits **Heartbeat (HB)** messages.

Heartbeat messages contain the same communication control information as ordinary protocol messages, including:

- Sender Identifier
- Receiver Identifier
- Sequence Number
- Confirmed Sequence Number
- Timestamp
- Confirmed Timestamp
- Security Code

Unlike data messages, heartbeat messages do not contain application payload.

Whenever ordinary application data is transmitted, that data message also fulfils the heartbeat function. Consequently, separate heartbeat messages are generated only when communication remains idle for longer than the configured heartbeat interval (**Th**).

This approach minimises unnecessary network traffic while maintaining continuous supervision of the communication channel.

---

## 4.21 Heartbeat Timer (Th)

The heartbeat interval is controlled by the configurable parameter **Th**.

Whenever a protocol message is transmitted, the heartbeat timer is restarted.

If no additional protocol message is generated before the timer expires, the Safety and Retransmission Layer automatically transmits a heartbeat message.

This guarantees that communication partners regularly exchange protocol information regardless of the amount of application traffic.

The heartbeat mechanism therefore serves two important purposes:

1. It confirms that both communication partners remain operational.
2. It continuously updates the adaptive timing calculations used for communication supervision.

Without heartbeat messages, adaptive channel monitoring would be impossible during periods of low application activity.

---

## 4.22 Flow Control

Communication partners may possess significantly different processing capabilities. A modern interlocking computer, for example, may generate protocol messages much faster than a remote signalling device can process them.

Without protection, this imbalance could eventually overflow the receiver's message buffer, resulting in message loss.

RaSTA prevents this situation through **Flow Control**.

During connection establishment, both communication partners exchange the value:

**Nsendmax**

which specifies the maximum number of unacknowledged messages that may exist simultaneously.

The sender continuously monitors the number of transmitted but unconfirmed protocol messages.

If this number reaches the receiver's advertised buffer capacity, additional transmissions are temporarily suspended until acknowledgements are received.

Flow control therefore guarantees that communication remains stable without sacrificing safety or requiring excessive receiver memory.

---

## 4.23 Maximum Messages Without Acknowledgement (MWA)

In addition to **Nsendmax**, the specification defines another important parameter:

**MWA (Messages Without Acknowledgement)**

MWA determines how many received messages may remain unacknowledged before the receiver actively generates an acknowledgement.

When the number of received messages reaches this threshold, the receiver immediately sends either:

- the next pending protocol message, or
- a dedicated heartbeat message.

This mechanism improves communication throughput by avoiding unnecessary waiting periods while simultaneously preventing sender-side congestion.

Consequently, RaSTA achieves high communication efficiency without compromising deterministic protocol behaviour.

---

## 4.24 Packetization

Applications frequently generate multiple small messages within a short period of time.

Transmitting every application message as an individual protocol packet would increase network overhead and reduce communication efficiency.

To optimise bandwidth utilisation, RaSTA supports **Packetization**.

Multiple application messages may be combined into a single protocol message before transmission.

Each application message is preceded by its own length field, allowing the receiver to reconstruct the original message sequence after reception.

The maximum number of application messages that may be combined into one protocol message is defined by the configuration parameter:

**NmaxPackage**

Although packetization improves transmission efficiency, the receiving application remains completely unaware that multiple messages were transmitted together.

The Safety and Retransmission Layer automatically separates the aggregated payload before delivering each application message individually.

This preserves complete transparency for the application software.

---

## 4.25 Retransmission Procedure

Even highly reliable communication networks may occasionally lose protocol messages.

Rather than immediately terminating communication after detecting a missing message, RaSTA first attempts to recover the missing information through a controlled retransmission procedure.

Retransmission begins when the receiver detects a sequence number discontinuity.

Instead of forwarding the unexpected message to the application, the protocol temporarily suspends message delivery and generates a **RetrReq (Retransmission Request)** message.

The retransmission request informs the sender of the last correctly received sequence number.

The sender then performs the following operations:

1. Searches its retransmission buffer for the missing message.
2. Sends a **RetrResp (Retransmission Response)** acknowledging the request.
3. Retransmits all missing application messages as **RetrData** messages.
4. Completes the retransmission process by transmitting either a normal data message or a heartbeat.

Only application data messages are retransmitted.

Connection management messages such as:

- Connection Request,
- Connection Response,
- Disconnect Request,

are never retransmitted because their behaviour is governed directly by the protocol state machine.

---

## 4.26 Safety Advantages of Retransmission

The retransmission procedure significantly improves communication availability without reducing functional safety.

Instead of disconnecting the communication channel after a single lost packet, the protocol first attempts to recover the missing information.

This approach provides several engineering advantages.

First, temporary communication disturbances can be corrected automatically without interrupting railway operation.

Second, retransmission preserves the original order of application data by ensuring that missing messages are delivered before later messages are released to the application.

Finally, retransmission operates under continuous supervision of the adaptive timing mechanism. If communication cannot be restored before the adaptive timeout expires, the protocol terminates the connection and enters the predefined safe state.

Consequently, retransmission improves communication availability while maintaining the fail-safe behaviour required for safety-related railway communication.

## 4.27 Protocol Error Handling

A fundamental design objective of RaSTA is that communication shall never continue if its correctness cannot be guaranteed. Every received protocol message therefore passes through a sequence of validation steps before it is accepted by the receiving application. These validation procedures ensure that any communication fault is detected as early as possible.

According to DIN VDE V 0831-200, every received protocol data unit is processed in the following order:

1. Verification of the Security Code.
2. Verification of the Sender and Receiver Identifiers.
3. Verification of the Message Type.
4. Sequence Number Range Check.
5. Exact Sequence Number Verification.
6. Confirmed Sequence Number Verification.
7. Timestamp Verification.
8. Processing by the Event-State Matrix.
9. Delivery to the Application Layer.

The order of these checks is not arbitrary. Less computationally expensive validation procedures are performed first, allowing obviously invalid messages to be discarded immediately. Only messages that successfully pass every verification stage are processed further by the protocol.

Whenever one of these validation steps fails, the message is discarded and the appropriate diagnostic error counter is incremented. Under no circumstances is an invalid message forwarded to the application layer.

This deterministic processing sequence contributes significantly to the safety integrity of the protocol because it guarantees consistent behaviour regardless of the origin of the communication fault.

---

## 4.28 Safe Disconnection

One of the most important characteristics of a safety-related communication protocol is its behaviour when communication can no longer be trusted.

Rather than attempting to continue communication under uncertain conditions, RaSTA intentionally terminates the communication session whenever protocol correctness can no longer be guaranteed.

The protocol issues a **Disconnect Request (DiscReq)** whenever one of several predefined fault conditions occurs.

The specification defines the following disconnection reasons:

| Reason Code | Description |
|-------------|-------------|
| 0 | User requested termination |
| 2 | Unexpected protocol message for the current state |
| 3 | Sequence number error during connection establishment |
| 4 | Communication timeout |
| 5 | Requested service not permitted in the current state |
| 6 | Protocol version mismatch |
| 7 | Retransmission failed because requested data is unavailable |
| 8 | Protocol flow violation |

**Table 4-7. Principal Disconnect Request reasons defined by the RaSTA specification.**

Each disconnection reason corresponds to a protocol condition for which safe communication can no longer be assured.

For example, if adaptive time monitoring determines that communication has exceeded the maximum acceptable delay, the connection is immediately terminated because the application can no longer rely on the freshness of the received information.

Similarly, if retransmission fails because the sender no longer possesses the requested data, message consistency cannot be restored and communication is therefore terminated.

By entering the predefined **Closed** state whenever these situations occur, RaSTA ensures that uncertainty never propagates beyond the communication protocol.

---

## 4.29 Error Counters and Diagnostics

Although communication safety is the primary objective of the protocol, RaSTA also provides extensive diagnostic capabilities that assist maintenance engineers during commissioning and operation.

Whenever one of the protocol verification procedures fails, the corresponding error counter is incremented.

The specification defines dedicated counters for:

| Error Counter | Monitored Error |
|---------------|-----------------|
| ECSafety | Invalid Security Code |
| ECAddress | Invalid Sender or Receiver Identifier |
| ECType | Undefined Message Type |
| ECSN | Invalid Sequence Number |
| ECCS | Invalid Confirmed Sequence Number |

**Table 4-8. Diagnostic error counters implemented by the Safety and Retransmission Layer.**

Unlike the communication safety mechanisms themselves, these counters do not influence protocol behaviour directly. Instead, they provide valuable statistical information regarding communication quality.

For example, a rapidly increasing ECSafety counter may indicate physical transmission errors or faulty communication hardware, whereas repeated ECSN increments may suggest excessive packet loss or network congestion.

This diagnostic information enables preventive maintenance before communication problems begin to affect railway operation.

---

## 4.30 Relationship to DIN EN 50159

The communication threats identified by DIN EN 50159 form the foundation upon which the RaSTA protocol has been designed.

Rather than relying on a single protective mechanism, RaSTA implements multiple independent safety functions that collectively address every significant communication threat identified by the standard.

Table 4-9 summarises the relationship between the principal communication threats and the corresponding RaSTA protection mechanisms.

| Communication Threat (DIN EN 50159) | RaSTA Protection Mechanism |
|------------------------------------|----------------------------|
| Message Corruption | MD4 Security Code |
| Message Repetition | Sequence Number Supervision |
| Message Loss | Retransmission Procedure |
| Message Insertion | Sender/Receiver Identification + Sequence Verification |
| Message Resequencing | Sequence Number Verification |
| Excessive Communication Delay | Adaptive Channel Monitoring |
| Communication Interruption | Heartbeat Supervision |
| Buffer Overflow | Flow Control |
| Obsolete Information | Timestamp Supervision |
| Incorrect Connection Establishment | Three-Way Handshake |

**Table 4-9. Mapping of DIN EN 50159 communication threats to RaSTA safety mechanisms.**

The table illustrates that no single protocol mechanism is solely responsible for communication safety. Instead, RaSTA employs several overlapping protection mechanisms, ensuring that the failure of one mechanism does not immediately compromise overall communication integrity.

This layered defence strategy is consistent with the engineering principles commonly applied throughout railway signalling systems.

---

## 4.31 Functional Safety Perspective

From a functional safety viewpoint, the mission-critical mechanisms implemented by RaSTA form multiple independent barriers against hazardous communication behaviour.

The protocol continuously verifies:

- who transmitted the message,
- whether the message was modified,
- whether messages arrive in the correct order,
- whether communication timing remains acceptable,
- whether communication partners remain operational,
- whether missing messages can be recovered,
- whether communication should be terminated.

Only when all these conditions are simultaneously satisfied is the received application data considered safe for further processing.

This approach reflects the fundamental principle of railway functional safety:

> **Communication shall never be assumed to be safe; it shall continuously prove that it remains safe.**

Rather than trusting the communication network, RaSTA continuously gathers evidence that communication remains valid throughout the lifetime of every connection. Whenever sufficient evidence can no longer be obtained, the protocol transitions deterministically into its predefined safe state by closing the connection.

## 4.32 Interaction of Mission-Critical Functions

One of the most significant strengths of the RaSTA protocol is that none of its safety mechanisms operates independently. Instead, every mission-critical function supports and reinforces the others, creating a layered communication safety architecture. This design ensures that the failure or limitation of one protection mechanism does not immediately compromise the overall safety of the communication system.

Figure 4-3 conceptually illustrates this layered defence philosophy.

**Connection Establishment** ensures that both communication partners begin communication with synchronized protocol parameters, compatible protocol versions, and agreed buffer configurations.

Once communication has been established, **Message Integrity Verification** confirms that every received protocol data unit remains identical to the transmitted message. Any modification introduced during transmission is detected before the message reaches the application.

At the same time, **Sequence Number Supervision** continuously monitors the order of incoming messages. Missing, duplicated, replayed, or incorrectly ordered messages are immediately identified, preventing incorrect protocol behaviour from propagating to higher software layers.

While sequence numbers guarantee logical communication correctness, **Adaptive Channel Monitoring** verifies the temporal correctness of communication. Even a perfectly ordered message is considered unsafe if it arrives after the maximum acceptable delay. Consequently, communication safety depends not only on message correctness but also on message freshness.

Whenever sequence supervision detects a missing protocol data unit, the **Retransmission Procedure** attempts to restore the complete communication history before any application data is released. This mechanism increases communication availability while preserving deterministic protocol behaviour.

During periods without application traffic, **Heartbeat Supervision** continues monitoring communication health. These periodic heartbeat messages provide continuous evidence that both communication partners remain operational and simultaneously update the adaptive timing calculations.

The **Flow Control Mechanism** prevents communication overload by ensuring that the sender never transmits more unacknowledged messages than the receiver can safely process. This protects against receiver buffer overflow without reducing communication reliability.

Finally, the **Safe Disconnection Mechanism** provides the ultimate safety barrier. Whenever one or more of the preceding mechanisms can no longer guarantee communication correctness, the protocol intentionally terminates the connection and enters the Closed state. This behaviour ensures that uncertain communication never reaches the railway signalling application.

Rather than acting as isolated protocol features, these mechanisms operate as a coordinated safety system in which every protocol message contributes simultaneously to integrity verification, sequence supervision, communication timing, acknowledgement processing, and connection monitoring.

---

## 4.33 Contribution to Functional Safety

From the perspective of functional safety engineering, the RaSTA protocol demonstrates several characteristics expected from safety-related communication systems operating in railway environments.

First, the protocol implements **systematic fault detection** rather than relying on fault tolerance alone. Communication faults are detected explicitly through independent verification mechanisms instead of assuming reliable network behaviour.

Second, the protocol follows a **fail-safe philosophy**. Whenever communication validity cannot be guaranteed, RaSTA immediately transitions to a predefined safe state by disconnecting the communication channel. The protocol therefore never attempts to "guess" whether uncertain information might still be valid.

Third, RaSTA employs **multiple independent protection mechanisms**. Message integrity, sequence supervision, adaptive timing, heartbeat monitoring, retransmission, and redundancy each contribute different forms of protection against communication hazards. This diversity significantly increases the robustness of the protocol and reduces the probability that multiple communication failures remain undetected simultaneously.

Finally, the protocol has been designed specifically to satisfy the communication safety principles defined in **DIN EN 50159**. Every major communication threat identified by the standard is addressed through one or more dedicated protocol mechanisms, providing a structured and traceable safety argument suitable for railway signalling applications.

These characteristics explain why RaSTA has become one of the preferred communication protocols for modern European railway signalling systems requiring high levels of communication integrity and availability.

---

## 4.34 Section Summary

This chapter examined the mission-critical functionality implemented by the Rail Safe Transport Application protocol. Unlike conventional communication protocols that depend primarily on reliable network infrastructure, RaSTA continuously supervises every aspect of communication using a comprehensive collection of protocol-level safety mechanisms.

The chapter first introduced the communication interfaces and services provided by the Safety and Retransmission Layer. It then examined adaptive channel monitoring, demonstrating how dynamic supervision timers provide more reliable communication monitoring than conventional fixed timeout methods.

Subsequently, the report analysed message integrity verification, protocol data units, sender and receiver identification, sequence number supervision, confirmed sequence numbers, heartbeat generation, flow control, packetization, retransmission procedures, diagnostic facilities, and safe disconnection mechanisms. Each mechanism was discussed not only from an implementation perspective but also with respect to its contribution to railway functional safety.

Finally, the relationship between the RaSTA protocol and the communication threats identified in DIN EN 50159 was established, demonstrating that the protocol systematically addresses message corruption, message loss, duplication, resequencing, delay, communication interruption, and protocol inconsistencies through multiple independent protection mechanisms.

The mission-critical functions presented in this chapter form the operational foundation of the RaSTA protocol. The following chapter builds upon these concepts by analysing the complete protocol architecture, communication state machines, connection establishment procedures, retransmission scenarios, and redundancy mechanisms defined by the official DIN VDE V 0831-200 specification.