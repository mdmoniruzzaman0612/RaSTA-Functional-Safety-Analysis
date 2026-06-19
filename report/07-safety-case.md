# 7. Safety Case

## 7.1 Safety Objective

The primary safety objective of RaSTA is to provide safe and highly available communication for railway signalling applications while satisfying the requirements of DIN EN 50159 [1].

The protocol shall ensure that communication failures cannot lead to the acceptance of incorrect, incomplete, delayed, duplicated, or unauthorized information by safety-related railway applications.

The fundamental safety principle of RaSTA is:

> Unsafe communication shall be detected before it can influence the application.

Whenever safe communication can no longer be guaranteed, the protocol transitions to a safe state by terminating the connection.

---

## 7.2 Communication Threats According to DIN EN 50159

DIN EN 50159 identifies several communication threats that must be addressed by safety-related communication systems.

The principal threats are:

| Threat | Description |
|----------|----------|
| Corruption | Modification of transmitted information |
| Repetition | Repeated transmission of previously valid data |
| Deletion | Loss of transmitted information |
| Delay | Information arrives too late |
| Insertion | Unauthorized creation of messages |
| Resequencing | Messages arrive in the wrong order |
| Masquerade | Communication partner impersonation |

**Table 7-1:** Communication threats defined by DIN EN 50159.

Failure to detect these threats could result in incorrect signalling decisions and potentially hazardous railway operating conditions.

---

## 7.3 Hazard Identification

The communication threats can be translated into system-level hazards.

| Hazard ID | Hazard Description | Potential Consequence |
|------------|------------------|----------------------|
| H-1 | Corrupted safety message accepted | Incorrect control action |
| H-2 | Delayed safety message accepted | Decision based on obsolete information |
| H-3 | Lost message not detected | Missing safety command |
| H-4 | Repeated message accepted | Duplicate execution of command |
| H-5 | Message sequence error undetected | Inconsistent system state |
| H-6 | Unauthorized sender accepted | Unsafe command execution |
| H-7 | Communication failure undetected | Loss of situational awareness |

**Table 7-2:** Hazard identification for RaSTA communication.

---

## 7.4 Safety Mechanisms Implemented by RaSTA

RaSTA implements multiple independent safety mechanisms to mitigate communication hazards.

| Threat | Safety Mechanism |
|----------|----------|
| Corruption | MD4 Safety Code |
| Repetition | Sequence Number Verification |
| Deletion | Sequence Number Monitoring and Retransmission |
| Delay | Timestamp and Adaptive Timeout Monitoring |
| Insertion | Sender and Receiver Identifier Validation |
| Resequencing | Sequence Number Verification |
| Masquerade | Sender and Receiver Identifier Validation |
| Channel Failure | Redundancy Layer |

**Table 7-3:** Mapping of threats to RaSTA safety mechanisms.

The use of multiple independent mechanisms increases fault detection coverage and reduces the likelihood of hazardous communication failures.

---

## 7.5 Integrity Protection Argument

Every Safety and Retransmission Layer message contains a safety code generated using MD4.

Upon reception:

1. The safety code is recalculated.
2. The received value is compared with the calculated value.
3. Messages failing verification are discarded.

This mechanism prevents corrupted messages from reaching the application layer.

Safety Argument:

```text
Message Corruption
        ↓
MD4 Verification
        ↓
Corruption Detected
        ↓
Message Rejected
        ↓
Safe State Maintained
```

---

## 7.6 Sequence Integrity Argument

Sequence numbers provide protection against:

- Repetition
- Replay
- Deletion
- Resequencing

The receiver continuously compares the received sequence number with the expected value.

Example:

```text
Expected SN = 25
Received SN = 26
```

Result:

```text
Sequence Error Detected
```

The protocol immediately initiates retransmission procedures.

Safety Argument:

```text
Missing Message
        ↓
Sequence Error
        ↓
Retransmission Request
        ↓
Data Recovery
        ↓
Safe Communication Restored
```

---

## 7.7 Timeliness Safety Argument

Correct information can become unsafe if delivered too late.

RaSTA continuously measures:

- Round-trip delay (Trtd)
- Connection supervision delay (Talive)

The adaptive timeout is calculated as:

```text
Ti = Trtd + Talive + Tmax
```

If the timer expires:

```text
DiscReq
        ↓
Closed State
```

The protocol therefore prevents stale information from reaching the application.

Safety Argument:

```text
Excessive Delay
        ↓
Adaptive Timeout
        ↓
Connection Termination
        ↓
Unsafe Data Prevented
```

---

## 7.8 Availability and Redundancy Argument

Communication availability is increased through redundant transport channels.

Example:

```text
Channel A
Channel B
Channel C
```

The same message is transmitted simultaneously on all available channels.

Consequently:

- Failure of a single transport channel does not necessarily interrupt communication.
- Availability increases with the number of independent channels.
- Safety behaviour remains unchanged.

Safety Argument:

```text
Single Channel Failure
        ↓
Alternative Channel Available
        ↓
Message Delivered
        ↓
Communication Maintained
```

---

## 7.9 Fault Tree Analysis

The top-level hazardous event considered in this analysis is:

```text
Unsafe Railway Communication
```

The corresponding fault tree is shown in Figure 7-2.

![Fault Tree Analysis](../figures/figure-7-2-fta.png)

**Figure 7-2:** Fault Tree Analysis of unsafe railway communication.

The top event may result from:

- Corrupted communication
- Lost communication
- Delayed communication
- Unauthorized communication

RaSTA interrupts these fault paths using:

- MD4 verification
- Sequence supervision
- Timeliness supervision
- Sender identification
- Redundancy mechanisms

The fault tree demonstrates that multiple independent failures would generally be required before unsafe communication could occur.

---

## 7.10 Residual Risks

Despite the extensive protection mechanisms provided by RaSTA, some residual risks remain.

Examples include:

### Complete Network Failure

Simultaneous failure of all transport channels can prevent communication.

Mitigation:

- Safe timeout detection
- Controlled connection termination

### Configuration Errors

Incorrect configuration of:

- Sender identifiers
- Receiver identifiers
- Timing parameters
- Security-code initialization values

may prevent correct operation.

Mitigation:

- Verification and validation activities
- Configuration management

### Application-Level Errors

RaSTA protects communication but does not validate application semantics.

Mitigation:

- Application-level safety analysis
- Independent functional verification

---

## 7.11 Safety Claim and Evidence

The safety argument for RaSTA can be summarized as follows.

### Claim

RaSTA provides safe communication for railway signalling applications.

### Evidence

- Safety-code verification
- Sequence-number supervision
- Timeliness monitoring
- Heartbeat supervision
- Retransmission procedures
- Redundancy mechanisms

### Verification

- Protocol specification analysis
- State-machine verification
- Retransmission scenarios
- Timing analysis
- Fault Tree Analysis

### Conclusion

The implemented mechanisms collectively detect communication failures before they can result in unsafe behaviour.

---

## 7.12 Safety Conclusion

The analysis demonstrates that RaSTA provides a comprehensive set of safety mechanisms addressing the communication threats identified by DIN EN 50159.

The protocol detects:

- Corruption
- Delay
- Message loss
- Message repetition
- Message resequencing
- Unauthorized communication

When communication validity cannot be guaranteed, RaSTA transitions to a safe state by terminating the connection.

Therefore, the protocol provides a suitable communication foundation for safety-related railway signalling systems and satisfies the safety objectives defined by DIN EN 50159 [1].