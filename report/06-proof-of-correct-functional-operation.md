# 6. Proof of Correct Functional Operation

## 6.1 Introduction

The previous chapter examined the internal architecture of the Rail Safe Transport Application (RaSTA) protocol, including its communication layers, message formats, protocol state machine, and redundancy mechanisms. While these architectural elements explain how the protocol is constructed, they do not demonstrate how the protocol behaves during actual communication.

To verify that the protocol satisfies its functional safety objectives, DIN VDE V 0831-200 defines several operational scenarios representing both normal communication and abnormal communication conditions. These scenarios illustrate the dynamic behaviour of the protocol throughout the complete communication lifecycle, including connection establishment, normal data exchange, retransmission, communication failures, and recovery procedures.

Rather than presenting isolated protocol functions, the scenarios demonstrate how multiple safety mechanisms interact during realistic communication events. Sequence supervision, adaptive timing, retransmission, heartbeat monitoring, and state transitions operate simultaneously to ensure that communication remains safe even when faults occur within the transmission network.

This chapter analyses these operational scenarios and evaluates how they demonstrate the correct functional operation of the protocol.

---

# 6.2 Verification Through Operational Scenarios

One of the strengths of the RaSTA specification is that it defines not only protocol structures but also complete communication scenarios illustrating expected protocol behaviour.

Each scenario serves as an executable example of the protocol specification.

Instead of describing isolated protocol functions, the standard demonstrates how multiple mechanisms interact under realistic operating conditions.

The scenarios analysed in this chapter include:

- Successful connection establishment
- Delayed connection response
- Delayed heartbeat
- Protocol version incompatibility
- Normal data exchange
- Successful retransmission
- Failed retransmission

Together, these scenarios demonstrate that the protocol behaves deterministically during both normal and abnormal operating conditions.

---

# 6.3 Functional Verification Criteria

The behaviour of each scenario is evaluated according to several functional safety criteria derived from DIN EN 50159.

The protocol shall demonstrate that it is capable of:

- Establishing synchronized communication.
- Detecting communication faults.
- Recovering from temporary transmission errors.
- Detecting communication interruption.
- Preventing obsolete information from reaching the application.
- Entering a predefined safe state whenever recovery is impossible.

These criteria form the basis for the following analysis.

---

# 6.4 Successful Connection Establishment

The first operational scenario demonstrates successful establishment of communication between two RaSTA instances.

![Successful Connection Establishment](../figures/figure-5-2-successful-connection-establishment.png)

**Figure 6-1. Successful connection establishment (adapted from DIN VDE V 0831-200 [1]).**

The communication begins when the client transmits a **Connection Request**.

The request contains the information required to initialize communication, including the protocol version, sender and receiver identifiers, random sequence number, receive buffer capacity, and communication timing information.

Upon successful verification of the received request, the server replies with a **Connection Response**.

This response confirms that both communication partners agree on the communication parameters required for subsequent protocol operation.

Finally, the client transmits a **Heartbeat** message.

Receipt of this heartbeat confirms that both communication partners have entered synchronized communication states.

Only after this three-stage handshake do both protocol instances transition into the **Up** state, allowing application messages to be exchanged.

This scenario demonstrates that communication begins only after complete synchronization has been achieved.

From a functional safety perspective, this prevents partially initialized communication sessions from influencing railway signalling applications.

# 6.5 Delayed Connection Response

The previous scenario demonstrated successful connection establishment under normal operating conditions. However, safety-related communication must also behave predictably when expected protocol messages are delayed or lost.

The second operational scenario defined by DIN VDE V 0831-200 examines the case where the **Connection Response** does not arrive within the permitted time interval.

![Delayed Connection Response](../figures/figure-6-2-delayed-connection-response.png)

**Figure 6-2. Failed connection establishment due to an excessive delay of the Connection Response (adapted from DIN VDE V 0831-200 [1]).**

After transmitting the Connection Request, the client starts a supervision timer while waiting for the corresponding Connection Response.

The protocol defines a maximum acceptable delay (**Tmax**) for reply messages. If the Connection Response does not arrive before this timeout expires, the connection attempt is considered unsuccessful.

Instead of waiting indefinitely, the client terminates the connection establishment procedure and returns to the **Closed** state.

No application data is exchanged because synchronization between the two communication partners has not yet been completed.

### Functional Safety Analysis

This behaviour prevents communication from being established using uncertain or incomplete protocol information.

Without a timeout mechanism, a delayed Connection Response might be accepted long after the communication context had become invalid, increasing the risk of synchronization errors or replayed messages.

By enforcing strict timing constraints during connection establishment, RaSTA guarantees that every communication session begins from a well-defined and synchronized state.

---

# 6.6 Delayed Heartbeat During Connection Establishment

Successful connection establishment requires not only the exchange of the Connection Request and Connection Response, but also the successful completion of the final Heartbeat message.

The following scenario illustrates communication failure caused by excessive delay of this Heartbeat.

![Delayed Heartbeat](../figures/figure-6-3-delayed-heartbeat.png)

**Figure 6-3. Failed connection establishment caused by an excessive Heartbeat delay (adapted from DIN VDE V 0831-200 [1]).**

After transmitting the Connection Response, the server waits for the Heartbeat generated by the client.

The Heartbeat confirms that both communication partners have completed synchronization and are ready for normal operation.

If this Heartbeat does not arrive before the supervision timer expires, the server assumes that communication has failed.

The protocol therefore terminates the connection establishment procedure and returns to the Closed state.

Since the synchronization process remains incomplete, application communication is never started.

### Functional Safety Analysis

The Heartbeat serves as more than a simple "keep-alive" message.

During connection establishment it acts as the final confirmation that both communication partners possess identical protocol information.

By refusing to enter the Up state until this confirmation has been received, RaSTA prevents partially synchronized communication sessions that could otherwise produce inconsistent protocol behaviour.

This demonstrates the protocol's deterministic approach to communication safety.

---

# 6.7 Client-Detected Protocol Error

Another important connection establishment scenario involves detection of protocol incompatibility during the handshake.

The following figure illustrates communication failure caused by a client-side protocol error.

![Client Detected Error](../figures/figure-6-4-client-detected-error.png)

**Figure 6-4. Connection establishment terminated after detection of a protocol error by the client (adapted from DIN VDE V 0831-200 [1]).**

After receiving the Connection Response, the client verifies several protocol parameters before accepting the connection.

Among the most important checks are:

- Protocol version compatibility
- Sender identifier
- Receiver identifier
- Message integrity
- Security code

If any of these verification procedures fails, the Connection Response is rejected.

Instead of attempting communication with incompatible protocol parameters, the client immediately sends a **Disconnect Request** and terminates the communication attempt.

The protocol therefore returns directly to the Closed state.

### Functional Safety Analysis

Protocol compatibility is essential for deterministic communication.

Even small differences in protocol interpretation could cause different implementations to process identical messages differently, resulting in unpredictable behaviour.

By validating protocol compatibility before entering the operational state, RaSTA guarantees that only compatible implementations exchange safety-related information.

This behaviour supports interoperability while simultaneously preventing unsafe communication caused by software incompatibility.

---

# 6.8 Evaluation of Connection Establishment

The four connection establishment scenarios analysed in this chapter demonstrate that RaSTA does not simply attempt to establish communication under all circumstances.

Instead, communication is permitted only when every stage of the synchronization procedure has been completed successfully.

Successful connection establishment requires:

- successful exchange of the Connection Request;
- successful exchange of the Connection Response;
- successful transmission of the synchronization Heartbeat;
- compatible protocol versions;
- valid protocol timing.

Failure of any one of these conditions causes the protocol to terminate communication immediately.

This conservative approach reflects the fail-safe philosophy of railway communication systems, where communication uncertainty is considered more dangerous than temporary communication interruption.

The analysed scenarios therefore provide practical evidence that the connection establishment procedure satisfies the communication safety principles defined by DIN EN 50159.

# 6.9 Normal Data Exchange

After successful completion of the connection establishment procedure, both communication partners enter the **Up** state and begin exchanging application data. During this operational phase, the protocol continuously supervises communication while simultaneously delivering application messages to the signalling system.

Unlike conventional communication protocols, every transmitted message contributes to both information exchange and communication supervision. Consequently, communication safety is continuously evaluated throughout the lifetime of the connection rather than only during connection establishment.

The following operational scenario illustrates normal communication between two synchronized RaSTA instances.

![Normal Data Exchange](../figures/figure-6-5-data-exchange.png)

**Figure 6-5. Normal exchange of application data and heartbeat messages (adapted from DIN VDE V 0831-200 [1]).**

Application messages are transmitted whenever new signalling information becomes available.

If no application data is generated during the configured heartbeat interval (**Th**), the protocol automatically transmits a Heartbeat message.

From the viewpoint of the receiver, both Data messages and Heartbeat messages are considered valid time-monitoring messages. Every correctly received message updates the communication timing information and restarts the adaptive supervision timer.

Consequently, communication remains continuously supervised even during long periods without application traffic.

The scenario also demonstrates that sequence numbers increase continuously throughout normal communication. Every successfully received message advances the expected receive sequence number, ensuring that the receiver always knows which message should arrive next.

### Functional Safety Analysis

This scenario demonstrates that RaSTA combines communication supervision with ordinary data transmission instead of treating them as separate protocol activities.

Application messages contribute simultaneously to:

- application data delivery;
- sequence number supervision;
- communication timing supervision;
- acknowledgement of previously received messages;
- verification of communication integrity.

This integrated approach minimises communication overhead while ensuring continuous functional safety monitoring.

---

# 6.10 Successful Retransmission

Although communication networks used within railway systems are generally highly reliable, temporary packet loss remains possible.

Rather than immediately terminating communication after detecting a missing message, RaSTA first attempts to recover the missing information using a controlled retransmission procedure.

The following operational scenario illustrates a successful retransmission.

![Successful Retransmission](../figures/figure-6-6-successful-retransmission.png)

**Figure 6-6. Successful retransmission following the loss of a protocol message (adapted from DIN VDE V 0831-200 [1]).**

In this example, message **25** is lost during transmission.

The next transmitted message (**26**) reaches the receiver successfully.

However, because the receiver expects sequence number **25**, the received message immediately fails sequence verification.

Instead of forwarding message 26 to the application, the protocol temporarily suspends application communication.

The receiver then performs the following sequence of operations:

1. Detects the sequence discontinuity.
2. Sends a **Retransmission Request (RetrReq)**.
3. Waits for the sender's response.
4. Receives the **Retransmission Response (RetrResp)**.
5. Receives the missing application messages as **RetrData**.
6. Restores communication synchronization.
7. Returns automatically to the **Up** state.

Throughout this process, no incomplete application data is delivered.

Only after all missing messages have been received successfully does the protocol continue normal communication.

### Functional Safety Analysis

This scenario demonstrates one of the principal advantages of the RaSTA protocol.

Instead of disconnecting after every isolated communication error, the protocol first attempts controlled recovery.

Communication availability therefore increases significantly because temporary packet loss no longer causes unnecessary interruption of signalling communication.

At the same time, communication safety remains uncompromised because application data is withheld until the original message sequence has been restored completely.

The retransmission procedure therefore represents an effective balance between availability and deterministic communication behaviour.

---

# 6.11 Timing Behaviour During Retransmission

An important aspect of the retransmission procedure concerns communication timing.

Even while retransmission is in progress, the protocol continues supervising message freshness using its adaptive timing mechanism.

The specification derives a relationship between the principal timing parameters:

- **Tmax** – Maximum acceptable message age
- **Th** – Heartbeat interval
- **TA** – Processing time of communication partner A
- **TB** – Processing time of communication partner B
- **Tseq** – Delay introduced by the Redundancy Layer

The standard specifies that the maximum acceptable communication delay should satisfy:

\[
T_{max} > 3T_h + 2(T_A + T_B) + T_{seq}
\]

This relationship ensures that retransmission can complete successfully before application information becomes obsolete.

Rather than relying on arbitrary timeout values, the protocol derives its communication timing from measurable communication behaviour.

This adaptive approach provides significantly greater robustness than conventional fixed-time supervision methods.

---

# 6.12 Failed Retransmission

Not every communication fault can be corrected successfully.

The following scenario demonstrates communication failure during the retransmission procedure.

![Failed Retransmission](../figures/figure-6-7-failed-retransmission.png)

**Figure 6-7. Communication failure caused by unsuccessful retransmission (adapted from DIN VDE V 0831-200 [1]).**

Initially, communication proceeds exactly as in the previous scenario.

A missing message is detected and the retransmission procedure begins normally.

However, during retransmission another protocol message is lost.

Consequently, the retransmitted message sequence itself becomes incomplete.

The receiver therefore detects a second sequence discontinuity while still recovering from the first communication failure.

The protocol immediately generates another Retransmission Request.

Meanwhile, the adaptive supervision timer continues counting.

Eventually, communication exceeds the maximum acceptable timing limit.

The supervision timer expires.

The protocol therefore concludes that communication correctness can no longer be guaranteed.

A Disconnect Request is transmitted and the communication session returns to the **Closed** state.

### Functional Safety Analysis

This scenario demonstrates one of the most important principles of functional safety engineering.

Recovery is attempted whenever communication can still be restored safely.

However, recovery is never allowed to continue indefinitely.

Once adaptive timing supervision determines that communication has become excessively delayed, the protocol intentionally abandons the recovery procedure and terminates communication.

This behaviour prevents outdated signalling information from reaching the railway application.

The protocol therefore prioritises communication correctness over communication availability.

---

# 6.13 Evaluation of Operational Behaviour

The operational scenarios analysed throughout this section demonstrate that the RaSTA protocol behaves deterministically under both normal and abnormal operating conditions.

During normal communication, every protocol message contributes simultaneously to information transfer, communication supervision, acknowledgement processing, and adaptive timing measurement.

When communication disturbances occur, the protocol first attempts controlled recovery through retransmission.

Only when recovery becomes impossible or communication timing exceeds the permitted safety limits does the protocol intentionally terminate the communication session.

This behaviour illustrates the layered defence philosophy of RaSTA.

Rather than relying on any single communication safeguard, the protocol combines sequence supervision, adaptive timing, heartbeat monitoring, retransmission, and deterministic state management into a coordinated communication safety strategy.

The analysed scenarios therefore provide practical evidence that the protocol satisfies the communication safety objectives defined by DIN EN 50159 while maintaining the high communication availability required for modern railway signalling systems.

# 6.14 Verification of Functional Safety Objectives

The operational scenarios presented throughout this chapter demonstrate that the RaSTA protocol consistently satisfies the functional safety objectives introduced in Chapter 3. Rather than relying on assumptions regarding the reliability of the communication network, the protocol continuously evaluates communication correctness throughout the entire communication session.

Each operational scenario illustrates the interaction of multiple protocol mechanisms. During normal communication, sequence supervision, adaptive timing, message integrity verification, and heartbeat monitoring operate simultaneously without requiring intervention from the application layer. When communication faults occur, retransmission procedures attempt to restore communication while adaptive timing supervision ensures that outdated information cannot remain valid indefinitely.

The protocol therefore provides two complementary properties that are essential for railway signalling systems:

- **Safety**, by ensuring that incorrect or outdated information is never delivered to the application.
- **Availability**, by recovering from temporary communication faults whenever safe recovery remains possible.

This balance between availability and safety represents one of the principal engineering strengths of the RaSTA protocol.

---

# 6.15 Compliance with DIN EN 50159

The communication threats identified in DIN EN 50159 provide the benchmark against which safety-related communication protocols are evaluated.

Table 6-1 summarises how the operational behaviour demonstrated in the previous scenarios addresses these threats.

| DIN EN 50159 Communication Threat | Operational Behaviour Demonstrated | RaSTA Response |
|----------------------------------|------------------------------------|----------------|
| Message corruption | Integrity verification during message reception | Corrupted messages are discarded immediately. |
| Message loss | Successful retransmission scenario | Missing messages are recovered before application delivery. |
| Message duplication | Sequence number supervision | Duplicate messages are rejected automatically. |
| Replay | Random initial sequence numbers and sequence verification | Previously transmitted messages are not accepted as valid. |
| Message resequencing | Sequence supervision and Defer Queue | Messages are delivered in the correct order. |
| Excessive delay | Adaptive timing supervision | Delayed messages trigger safe disconnection when timing limits are exceeded. |
| Communication interruption | Heartbeat supervision | Missing communication is detected through timeout monitoring. |
| Protocol incompatibility | Connection establishment verification | Incompatible implementations are prevented from communicating. |

**Table 6-1. Relationship between operational scenarios and the communication threats defined in DIN EN 50159.**

The analysed scenarios demonstrate that every principal communication threat identified by the standard is addressed by one or more protocol mechanisms. More importantly, no single mechanism is solely responsible for communication safety. Instead, multiple independent checks operate together to provide layered protection against communication faults.

---

# 6.16 Discussion

The operational scenarios reveal several important characteristics of the RaSTA protocol.

First, communication is deterministic. Every protocol event results in a predefined response, allowing different implementations to behave identically under identical operating conditions. This determinism simplifies interoperability testing and supports formal safety certification.

Second, communication recovery is conservative. The protocol attempts retransmission whenever communication can still be restored safely, thereby improving system availability. However, recovery is never allowed to continue indefinitely. Adaptive timing supervision continuously evaluates message freshness, ensuring that obsolete information is never accepted simply because recovery is technically possible.

Third, the protocol follows a strict fail-safe philosophy. Whenever the correctness of communication can no longer be guaranteed, the protocol intentionally terminates the communication session rather than risking the delivery of uncertain information. Although this may temporarily reduce system availability, it preserves the integrity of the railway signalling application, which is the primary objective of functional safety.

Finally, the analysed scenarios demonstrate that the Safety and Retransmission Layer and the Redundancy Layer operate as complementary components. While retransmission improves the probability of successful communication, redundancy reduces the likelihood that retransmission becomes necessary in the first place by providing multiple independent transport paths.

---

# 6.17 Chapter Summary

This chapter analysed the dynamic behaviour of the Rail Safe Transport Application protocol using the operational scenarios defined in DIN VDE V 0831-200. These scenarios demonstrated how the protocol behaves during successful communication, communication delays, protocol incompatibilities, normal data exchange, retransmission, and communication failure.

The analysis showed that RaSTA continuously supervises communication using multiple independent mechanisms, including message integrity verification, sequence number monitoring, heartbeat supervision, adaptive timing, retransmission, and deterministic state transitions. These mechanisms cooperate to detect communication faults before they influence railway signalling applications.

The retransmission scenarios illustrated that the protocol prioritises safe recovery whenever communication can still be restored. When recovery is no longer possible, adaptive timing supervision ensures that the communication session is terminated before outdated information can be delivered to the application. This behaviour demonstrates the protocol's fail-safe design philosophy and its compliance with the communication safety principles defined by DIN EN 50159.

Having established that the protocol operates correctly under both normal and abnormal conditions, the next chapter develops the **Safety Case** by evaluating how the combined protocol mechanisms provide evidence that RaSTA satisfies the functional safety requirements expected of modern railway signalling communication systems.