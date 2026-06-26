# Functional Safety Analysis of the Rail Safe Transport Application (RaSTA) Protocol

## Functional Safety Design

### Submitted by

**Md Moniruzzaman**

Bachelor of Science in Information Engineering

HAW Hamburg – Hamburg University of Applied Sciences

---

### Course

Functional Safety Design

### Professor

<Professor Name>

### Submission Date

<Date>

---

# Table of Contents

> Automatically generated.

---

# List of Figures

| Figure | Description |
|---------|-------------|
| Figure 2-1 | Classification and delimitation of the RaSTA specification |
| Figure 2-2 | RaSTA protocol stack |
| Figure 2-3 | Protocol interfaces |
| Figure 4-1 | Adaptive channel monitoring |
| Figure 5-1 | Layered architecture |
| Figure 5-2 | Successful connection establishment |
| Figure 5-3 | Safety Layer state machine |
| Figure 5-4 | Redundancy channel model |
| Figure 5-5 | Redundancy Layer state machine |
| Figure 6-1 | Successful connection establishment |
| Figure 6-2 | Delayed Connection Response |
| Figure 6-3 | Delayed Heartbeat |
| Figure 6-4 | Client Detected Error |
| Figure 6-5 | Normal Data Exchange |
| Figure 6-6 | Successful Retransmission |
| Figure 6-7 | Failed Retransmission |

---

# List of Tables

| Table | Description |
|--------|-------------|
| Table 2-1 | Responsibilities of protocol layers |
| Table 3-1 | Communication threats |
| Table 3-2 | Requirement mapping |
| Table 4-1 | Mission-critical mechanisms |
| Table 4-2 | Threats and mitigation |
| Table 5-1 | Protocol Data Unit |
| Table 5-2 | Protocol message types |
| Table 5-3 | Connection Request fields |
| Table 5-4 | Connection Response fields |
| Table 5-5 | Safety Layer states |
| Table 5-6 | Confirmed sequence variables |
| Table 5-7 | Retransmission states |
| Table 5-8 | Redundancy Layer states |
| Table 5-9 | Redundancy functions |
| Table 6-1 | DIN EN 50159 operational verification |
| Table 7-1 | Communication hazards |
| Table 7-2 | Hazard mitigation mechanisms |
| Table 7-3 | Safety case evidence |

---

# List of Abbreviations

| Abbreviation | Meaning |
|--------------|---------|
| CRC | Cyclic Redundancy Check |
| CS | Confirmed Sequence Number |
| CTS | Confirmed Timestamp |
| DIN | Deutsches Institut für Normung |
| EN | European Standard |
| HB | Heartbeat |
| IEC | International Electrotechnical Commission |
| MD4 | Message Digest Algorithm 4 |
| MWA | Maximum Messages Without Acknowledgement |
| PDU | Protocol Data Unit |
| RaSTA | Rail Safe Transport Application |
| RAMS | Reliability, Availability, Maintainability and Safety |
| RFC | Request for Comments |
| SN | Sequence Number |
| TCP | Transmission Control Protocol |
| TS | Timestamp |
| UDP | User Datagram Protocol |

---

# Document Structure

1. Executive Summary
2. System Overview
3. System Requirements
4. Mission Critical Functionality
5. System Architecture
6. Proof of Correct Functional Operation
7. Safety Case
8. Summary
9. References