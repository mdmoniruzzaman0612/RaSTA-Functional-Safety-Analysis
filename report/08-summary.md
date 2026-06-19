# 8. Summary

## 8.1 Report Overview

This report presented a functional safety analysis of the Rail Safe Transport Application (RaSTA) protocol specified in DIN VDE V 0831-200 Version 03.03.

RaSTA was developed to provide safe and highly available communication for railway signalling applications while satisfying the requirements of DIN EN 50159. The protocol achieves this objective through a layered architecture consisting of a Safety and Retransmission Layer and a Redundancy Layer operating over conventional transport services such as UDP and TCP.

The analysis focused on the protocol architecture, communication mechanisms, safety functions, verification of protocol behaviour, and safety argumentation.

---

## 8.2 Main Findings

The analysis identified several key mechanisms that contribute to the safety and availability of RaSTA communication.

### Safe Connection Establishment

RaSTA establishes communication using a controlled handshake procedure consisting of:

```text
ConnReq
   →
ConnResp
   →
HB
```

This procedure ensures that communication parameters are synchronized before operational communication begins.

### Message Integrity Protection

The protocol uses MD4-based safety codes to detect message corruption before data is delivered to the application.

### Sequence Integrity Protection

Sequence numbers and confirmed sequence numbers allow detection of:

- Message loss
- Message repetition
- Replay
- Resequencing
- Message insertion

### Timeliness Monitoring

Adaptive timeout supervision ensures that outdated information is not accepted by the receiving application.

### Retransmission Capability

Lost messages can be recovered through the RetrReq / RetrResp / RetrData mechanism without violating message ordering.

### Communication Redundancy

The Redundancy Layer improves communication availability by using multiple transport channels and constructing a single logical communication channel from them.

---

## 8.3 Compliance with DIN EN 50159

The protocol mechanisms were evaluated against the communication threats identified in DIN EN 50159.

The analysis demonstrated that RaSTA provides protection against:

| Communication Threat | RaSTA Mechanism |
|----------------------|----------------|
| Corruption | MD4 Safety Code |
| Delay | Timeliness Monitoring |
| Repetition | Sequence Supervision |
| Deletion | Retransmission |
| Insertion | Sender and Receiver Validation |
| Resequencing | Sequence Supervision |
| Masquerade | Identifier Validation |

**Table 8-1:** Mapping between DIN EN 50159 threats and RaSTA protections.

---

## 8.4 Verification Results

The protocol behaviour was examined using:

- Connection establishment scenarios
- Sequence supervision analysis
- Timeliness monitoring analysis
- Retransmission scenarios
- Redundancy-layer operation
- Fault handling mechanisms
- Fault Tree Analysis

The results demonstrated that communication failures are either corrected through retransmission or detected and handled through safe connection termination.

---

## 8.5 Safety Assessment

The safety mechanisms implemented by RaSTA provide multiple independent layers of protection.

These mechanisms include:

- Integrity verification
- Sender validation
- Receiver validation
- Sequence verification
- Timeliness supervision
- Heartbeat supervision
- Retransmission procedures
- Redundancy management

The protocol therefore exhibits the characteristics expected of a safety-related communication system used in railway signalling environments.

---

## 8.6 Conclusion

The functional safety analysis demonstrates that RaSTA provides a robust communication framework for railway signalling systems.

The protocol successfully combines:

- Functional safety
- Communication integrity
- Availability enhancement
- Fault detection
- Safe failure behaviour

The implemented mechanisms ensure that communication faults are detected before unsafe information can influence railway signalling applications.

Consequently, RaSTA provides a suitable and standards-compliant communication solution for safety-related railway systems operating under the requirements of DIN EN 50159.