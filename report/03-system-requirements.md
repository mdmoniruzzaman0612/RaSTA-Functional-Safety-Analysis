# 3. System Requirements

## 3.1 Introduction

This section defines the functional, safety, availability, timing, diagnostic, and configuration requirements for a RaSTA-based communication system. The requirements are derived from the RaSTA protocol specification, DIN VDE V 0831-200 Version 03.03, and from the safety communication objectives of DIN EN 50159 [1].

The purpose of these requirements is to establish a traceable basis for the subsequent hazard analysis, architecture analysis, verification activities, and safety case.

---

## 3.2 Functional Requirements

### FR-1 Safe Communication Service

The system shall provide a safe communication service for railway signalling applications using the RaSTA protocol stack [1].

### FR-2 Application Independence

The communication protocol shall be independent of the specific railway application protocol [1].

### FR-3 Connection Establishment

The system shall provide a connection establishment mechanism using Connection Request, Connection Response, and Heartbeat messages [1].

### FR-4 Data Transmission

The system shall support transmission of application messages using RaSTA Data messages [1].

### FR-5 Retransmission

The system shall support retransmission of lost or corrupted user data using Retransmission Request, Retransmission Response, and Retransmitted Data messages [1].

### FR-6 Heartbeat Supervision

The system shall send Heartbeat messages when no data has been transmitted within the configured heartbeat interval (T_h) [1].

### FR-7 Redundant Transmission

The Redundancy Layer shall transmit each message identically over all available transport channels [1].

### FR-8 Transport Independence

The protocol shall support operation over standard transport services such as UDP or TCP [1].

---

## 3.3 Safety Requirements

### SR-1 Message Integrity Verification

The receiver shall verify the safety code of each received Safety and Retransmission Layer message before further processing [1].

### SR-2 Sender and Receiver Validation

The receiver shall verify that the sender and receiver identifiers match the expected RaSTA connection [1].

### SR-3 Message Type Validation

The receiver shall reject undefined message types [1].

### SR-4 Sequence Number Supervision

The receiver shall verify the sequence number of received messages to detect repetition, replay, omission, loss, insertion, and resequencing [1].

### SR-5 Confirmed Sequence Number Supervision

The receiver shall verify the confirmed sequence number to ensure that acknowledgements are plausible [1].

### SR-6 Timeliness Monitoring

The receiver shall verify timeliness of time-monitoring-relevant messages using timestamp and confirmed timestamp information [1].

### SR-7 Connection Failure Detection

The receiver shall terminate the connection when the adaptive monitoring timer (T_i) expires [1].

### SR-8 Safe Disconnection

If the protocol detects an unrecoverable error, the connection shall be closed using a Disconnect Request message with an appropriate reason code [1].

---

## 3.4 Availability Requirements

### AR-1 Redundancy Channel

The system shall support a redundancy channel consisting of one or more transport channels [1].

### AR-2 Multi-Channel Transmission

The sender shall transmit each redundancy-layer message on all available transport channels [1].

### AR-3 Duplicate Handling

The redundancy-layer receiver shall detect duplicate messages received from multiple transport channels [1].

### AR-4 Out-of-Sequence Buffering

The redundancy-layer receiver shall temporarily store out-of-sequence messages in a DeferQueue [1].

### AR-5 Deferred Delivery

Messages stored in the DeferQueue shall be delivered when missing earlier messages arrive or when the configured timeout (T_{seq}) expires [1].

---

## 3.5 Timing Requirements

### TR-1 Maximum Message Age

The maximum acceptable age of a message shall be defined by the configuration parameter (T_{max}) [1].

### TR-2 Heartbeat Interval

Heartbeat messages shall be sent when no message has been sent within the configured interval (T_h) [1].

### TR-3 Adaptive Monitoring Timer

The adaptive monitoring timer (T_i) shall be recalculated after reception of a time-monitoring-relevant message [1].

### TR-4 Timing Constraint for Retransmission

The timing parameters shall satisfy the following relationship [1]:

```text
Tmax > 3 × Th + 2 × (TA + TB) + Tseq
```

where:

* (T_{max}) = maximum acceptable message age
* (T_h) = heartbeat interval
* (T_A) = transmission time from A to B
* (T_B) = transmission time from B to A
* (T_{seq}) = redundancy-layer defer time

---

## 3.6 Diagnostic Requirements

### DR-1 Error Counters

The system shall maintain diagnostic error counters for:

* Incorrect safety code
* Invalid sender or receiver identifier
* Undefined message type
* Implausible sequence number
* Implausible confirmed sequence number [1]

### DR-2 Channel Quality Monitoring

The system shall support diagnostic monitoring of channel quality using (T_{rtd}) and (T_{alive}) values [1].

### DR-3 Transport Channel Diagnostics

The Redundancy Layer shall provide diagnostic information for each transport channel using values such as (N_{missed}), (T_{drift}), and (T_{drift2}) [1].

---

## 3.7 Configuration Requirements

The system configuration shall include the following Safety and Retransmission Layer parameters:

| Parameter        | Meaning                                                           |
| ---------------- | ----------------------------------------------------------------- |
| (T_{max})        | Maximum acceptable age of a message                               |
| (T_h)            | Heartbeat interval                                                |
| Security Code    | None, lower half of MD4, or full MD4                              |
| MWA              | Maximum number of received unacknowledged messages                |
| (N_{sendmax})    | Receive buffer capacity exchanged during connection establishment |
| (N_{maxPackage}) | Maximum packetization factor                                      |
| (N_{diagWindow}) | Diagnostic measurement window                                     |

**Table 3-1:** Safety and Retransmission Layer configuration parameters [1].

The Redundancy Layer configuration shall include:

| Parameter                   | Meaning                                     |
| --------------------------- | ------------------------------------------- |
| Number of Physical Channels | Number of transport paths used              |
| Verification Code           | None, CRC32, CRC32C, CRC16                  |
| (T_{seq})                   | Time for buffering out-of-sequence messages |
| (N_{diagnosis})             | Diagnostic measurement window               |
| (N_{deferQueueSize})        | Maximum DeferQueue size                     |

**Table 3-2:** Redundancy Layer configuration parameters [1].

---

## 3.8 Requirement Traceability Foundation

The requirements defined in this section provide the basis for the remainder of the report.

```text
Requirements
      ↓
Hazards
      ↓
Safety Functions
      ↓
RaSTA Mechanisms
      ↓
Verification
      ↓
Safety Case
```

This traceability ensures that the protocol analysis remains connected to identifiable safety, timing, diagnostic, and availability requirements.
