# 6. Proof of Correct Functional Operation

## 6.1 Introduction

The objective of this section is to demonstrate that RaSTA performs its intended communication functions correctly while maintaining compliance with the safety requirements of railway signalling systems. The analysis is based on the protocol specification defined in DIN VDE V 0831-200 Version 03.03 and evaluates the protocol behaviour during connection establishment, normal operation, retransmission, redundancy management, and fault handling [1].

Correct functional operation is demonstrated by examining the protocol mechanisms and verifying that each mechanism contributes to safe and highly available communication.

---

## 6.2 Verification of Connection Establishment

Before user data can be exchanged, RaSTA requires successful completion of a three-message handshake procedure.

The connection establishment sequence is:

```text
Client                           Server

ConnReq ----------------------->

                    <----------- ConnResp

HB ---------------------------->

Connection State = Up
```

The client initiates communication by transmitting a Connection Request (ConnReq). The server responds with a Connection Response (ConnResp). The client then confirms acceptance by sending a Heartbeat (HB) message. Only after completion of this sequence does the connection enter the operational state "Up" [1].

During this procedure the protocol exchanges:

- Protocol version
- Sender identifier
- Receiver identifier
- Initial sequence numbers
- Receive buffer size (Nsendmax)
- Timestamp information

The exchange of these parameters ensures both communication partners operate using synchronized protocol data.

### Verification Result

The protocol establishes communication only after successful completion of the handshake procedure. Partial or incomplete connection establishment cannot result in operational communication. Therefore, the connection establishment mechanism satisfies its functional objective [1].

---

## 6.3 Verification of Message Integrity

Every Safety and Retransmission Layer message contains a safety code generated from the protocol data unit.

RaSTA supports the following integrity options:

| Option | Integrity Mechanism |
|----------|----------|
| 1 | No safety code |
| 2 | Lower half of MD4 |
| 3 | Full MD4 |

**Table 6-1:** Safety-code options [1].

Upon reception:

1. The safety code is recalculated.
2. The received value is compared with the calculated value.
3. Invalid messages are discarded.

### Verification Result

Any modification of protected message contents causes a safety-code mismatch and prevents the message from being accepted. The integrity mechanism therefore detects corrupted messages before they can reach the application layer [1].

---

## 6.4 Verification of Sequence Integrity

RaSTA uses sequence numbers to ensure correct message ordering and to detect communication failures.

Each protocol data unit contains:

- Sequence Number (SN)
- Confirmed Sequence Number (CS)

The sender increments SN for every transmitted message.

The receiver verifies:

```text
Expected Sequence Number
=
Received Sequence Number
```

Messages arriving with unexpected sequence numbers are treated as sequence errors.

Sequence supervision detects:

- Message loss
- Message repetition
- Message replay
- Message insertion
- Message resequencing

### Example

Expected:

```text
SN = 25
```

Received:

```text
SN = 26
```

Result:

```text
Sequence Error Detected
```

Retransmission procedure initiated.

### Verification Result

Sequence supervision prevents invalid message ordering and enables reliable detection of communication anomalies [1].

---

## 6.5 Verification of Timeliness Monitoring

Safe railway communication requires information to be both correct and sufficiently recent.

RaSTA implements adaptive timeliness monitoring using:

- Timestamp (TS)
- Confirmed Timestamp (CTS)
- Round-trip delay measurement (Trtd)
- Connection supervision delay (Talive)

The supervision timer is calculated as:

```text
Ti = Trtd + Talive + Tmax
```

where:

- Ti = adaptive timeout
- Tmax = maximum acceptable message age

The timer is continuously updated during operation.

### Verification Result

The protocol continuously monitors communication delay and automatically terminates connections when messages become excessively old. This prevents stale information from influencing safety-related applications [1].

---

## 6.6 Verification of Heartbeat Supervision

Heartbeat messages provide continuous connection supervision.

When no application data has been transmitted during the configured interval Th, a Heartbeat message is sent automatically.

Heartbeat messages perform the following functions:

- Confirm remote availability
- Update timing measurements
- Maintain connection supervision
- Prevent unnecessary connection termination

Message type:

```text
HB (6220)
```

### Verification Result

Heartbeat supervision allows communication failures to be detected even during periods when no application data is exchanged. Therefore, the protocol remains observable under all operating conditions [1].

---

## 6.7 Verification of Retransmission Behaviour

Message loss is detected through sequence-number supervision.

When a missing sequence number is identified, the receiver initiates retransmission.

Retransmission procedure:

```text
RetrReq
      →
RetrResp
      →
RetrData
```

The sender retransmits all unacknowledged application data beginning with the missing sequence number.

### Example

Normal transmission:

```text
24
25
26
```

Lost message:

```text
25
```

Received:

```text
24
26
```

Result:

```text
RetrReq Generated
```

Sender retransmits:

```text
RetrData(25)
RetrData(26)
```

Communication then returns to normal operation.

### Verification Result

The retransmission mechanism restores communication after message loss while maintaining sequence integrity and message ordering [1].

---

## 6.8 Verification of Redundancy Operation

The Redundancy Layer improves communication availability through the use of multiple transport channels.

Example:

```text
Transport Channel A
Transport Channel B
Transport Channel C
```

A single logical redundancy channel is formed from these transport channels.

Every transmitted message is sent simultaneously over all available channels using:

- Identical payload
- Identical sequence number
- Identical CRC value

### Verification Result

Failure of an individual transport channel does not necessarily interrupt communication because alternative copies of the message may still arrive through the remaining channels [1].

---

## 6.9 Verification of DeferQueue Operation

Messages may arrive out of sequence because transport channels have different transmission delays.

The Redundancy Layer stores such messages in a DeferQueue.

Example:

```text
Expected: 10

Received:
11
12
```

Messages 11 and 12 are temporarily stored.

When message 10 arrives:

```text
10
```

the queue is released and normal ordering is restored.

If the missing message does not arrive before timeout Tseq expires, the stored message is released.

### Verification Result

The DeferQueue improves availability while preserving deterministic message ordering [1].

---

## 6.10 Verification of Fault Handling

RaSTA responds to protocol errors through defined fault-handling mechanisms.

Examples include:

| Fault | Protocol Response |
|---------|---------|
| Invalid safety code | Message discarded |
| Invalid sender ID | Message discarded |
| Invalid message type | Message discarded |
| Sequence error | Retransmission initiated |
| Timeliness violation | Connection terminated |
| Retransmission failure | Connection terminated |
| Invalid protocol version | Connection terminated |

**Table 6-2:** Fault handling behaviour [1].

When safe communication can no longer be guaranteed, RaSTA transmits a Disconnect Request (DiscReq) and enters the Closed state.

### Verification Result

The protocol fails safely by terminating communication rather than allowing potentially unsafe information to reach the application [1].

---

## 6.11 Summary

The analysis demonstrates that RaSTA correctly performs its intended communication functions.

The protocol provides:

- Verified connection establishment
- Message integrity protection
- Sequence supervision
- Timeliness monitoring
- Heartbeat supervision
- Retransmission of lost data
- Communication redundancy
- Safe fault handling

These mechanisms operate together to provide safe and highly available communication for railway signalling systems. The protocol therefore satisfies its functional objectives and provides the technical foundation required for compliance with DIN EN 50159 [1].