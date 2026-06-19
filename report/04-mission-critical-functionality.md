# 4. Mission-Critical Functionality

## 4.1 Introduction

RaSTA is a safety-related communication protocol developed for railway signalling systems. The protocol is designed to ensure that safety-critical information can be exchanged reliably between distributed railway control systems while satisfying the requirements of DIN EN 50159 [1].

Unlike conventional communication protocols, RaSTA incorporates multiple safety mechanisms that detect communication failures before they can affect railway operations. These mechanisms operate continuously throughout connection establishment, normal operation, retransmission, and connection termination.

The mission-critical functions of RaSTA can be grouped into:

1. Safe connection establishment
2. Message integrity protection
3. Sequence supervision
4. Timeliness supervision
5. Connection supervision
6. Retransmission of lost messages
7. Communication redundancy
8. Safe connection termination

---

## 4.2 Safe Connection Establishment

Before application data can be exchanged, a communication session must be established between two RaSTA instances.

RaSTA uses a three-step handshake procedure consisting of:

```text
Connection Request (ConnReq)
        →
Connection Response (ConnResp)
        →
Heartbeat (HB)
```

Only after successful completion of this sequence does the connection enter the **Up** state [1].

The protocol automatically assigns client and server roles based on the sender and receiver identifiers:

- Lower identifier → Client
- Higher identifier → Server

During connection establishment the following information is exchanged:

- Protocol version
- Receive buffer size (Nsendmax)
- Sender identifier
- Receiver identifier
- Initial sequence numbers
- Timestamp information

This process ensures that both communication partners begin operation using synchronized protocol parameters [1].

---

## 4.3 Message Integrity Protection

Every Safety and Retransmission Layer message contains a safety code calculated from the message contents.

RaSTA supports three integrity options:

| Option | Safety Code |
|----------|----------|
| Option 1 | No safety code |
| Option 2 | Lower 64 bits of MD4 |
| Option 3 | Full 128-bit MD4 |

**Table 4-1:** RaSTA safety code options [1].

The safety code is calculated over the complete protocol data unit excluding the safety code field itself.

When a message is received:

1. The safety code is recalculated.
2. The received and calculated values are compared.
3. Messages with invalid safety codes are discarded.

This mechanism protects against:

- Data corruption
- Message modification
- Invalid message insertion

[1]

## 4.4 Sequence Supervision

RaSTA uses sequence numbers to detect communication anomalies.

Each transmitted message contains:

| Field | Purpose |
|---------|---------|
| SN | Sequence Number |
| CS | Confirmed Sequence Number |

**Table 4-2:** Sequence-related fields [1].

The sender increments the sequence number for every transmitted message.

The receiver verifies:

```text
Expected Sequence Number
=
Received Sequence Number
```

If the expected value differs from the received value, a sequence error is detected.

Sequence supervision protects against:

- Message repetition
- Replay attacks
- Message omission
- Message insertion
- Message loss
- Message resequencing

[1]

---

## 4.5 Timeliness Supervision

Safe railway communication requires not only correct information but also timely information.

RaSTA implements adaptive timeliness monitoring using:

- Timestamp (TS)
- Confirmed Timestamp (CTS)
- Round-trip delay measurements
- Adaptive timeout calculation

The protocol continuously calculates:

```text
Trtd
```

Round-trip delay time.

and

```text
Talive
```

Connection supervision delay.

The adaptive timeout timer is then calculated as:

```text
Ti = Trtd + Talive + Tmax
```

where:

- Ti = supervision timeout
- Tmax = maximum acceptable message age

[1]

If timer Ti expires, the connection is considered unsafe and is immediately terminated.

---

## 4.6 Heartbeat Supervision

When no application data is available, RaSTA transmits heartbeat messages.

Heartbeat messages:

- Maintain connection supervision
- Confirm remote availability
- Update timing measurements
- Prevent timeout expiration

Heartbeat messages are transmitted when no message has been sent within:

```text
Th
```

heartbeat interval.

[1]

## 4.7 Retransmission of Lost Messages

Lost messages are detected through sequence supervision.

Example:

```text
Expected SN = 25
Received SN = 26
```

A sequence error is detected because message 25 is missing.

The receiver then initiates a retransmission procedure:

```text
RetrReq
      →
RetrResp
      →
RetrData
```

The sender retransmits all unacknowledged application data beginning with the missing message [1].

This mechanism improves communication availability while preserving message ordering.

---

## 4.8 Communication Redundancy

The Redundancy Layer provides high availability through multiple transport channels.

A redundancy channel may be implemented using:

```text
Channel A
Channel B
Channel C
...
```

The same message is transmitted on every transport channel using the same sequence number and CRC value [1].

If one transport channel loses or corrupts a message, another channel can still provide a valid copy.

This architecture significantly improves communication availability without affecting safety behaviour.

---

## 4.9 Safe Disconnection

When safe communication can no longer be guaranteed, RaSTA terminates the connection.

Examples include:

- Timeout expiration
- Invalid protocol version
- Sequence number errors
- Retransmission failure
- Invalid protocol flow

Connection termination is performed using:

```text
DiscReq
```

with an associated reason code [1].

By entering the Closed state, the protocol ensures that potentially unsafe communication is prevented from reaching the application.

## 4.10 Summary

The mission-critical functionality of RaSTA is based on multiple independent safety mechanisms operating simultaneously.

| Safety Objective | RaSTA Mechanism |
|------------------|----------------|
| Message Integrity | MD4 Safety Code |
| Message Authenticity | Sender/Receiver IDs |
| Sequence Integrity | SN and CS |
| Timeliness | TS, CTS, Ti |
| Connection Supervision | Heartbeats |
| Message Recovery | Retransmission |
| Availability | Redundancy Layer |
| Safe Failure | DiscReq and Closed State |

**Table 4-3:** Mapping of safety objectives to protocol mechanisms.

These mechanisms collectively provide the foundation for safe railway communication according to DIN EN 50159 [1].
