# 8 Summary

## 8.1 Project Objectives

The objective of this project was to investigate the Rail Safe Transport Application (RaSTA) as a safety-related communication protocol for railway systems and to evaluate its suitability for the safe transmission of information over non-safe communication networks [6].

The study was performed within the framework of Functional Safety and the RAMS lifecycle defined by IEC 61508 and EN 50126 [1][3]. Particular attention was given to communication hazards that may affect safety-related railway applications and to the mechanisms implemented by RaSTA to detect and mitigate these hazards [5][6].

The project aimed to:

* Define communication requirements.
* Identify communication hazards.
* Perform risk assessment.
* Derive safety functions.
* Analyse the RaSTA architecture.
* Evaluate reliability and availability.
* Develop a structured Safety Case.

These objectives were achieved through the activities described throughout the report.

---

## 8.2 Main Results

### Requirements Analysis

A complete set of functional, performance, reliability, availability, maintainability, and safety requirements was established for the communication system [1][3].

These requirements provided measurable objectives and formed the basis for subsequent hazard analysis, architecture development, and verification activities.

---

### Hazard Analysis and Risk Assessment

Eight major communication hazards were identified based on the communication threat categories defined by EN 50159 [5].

The identified hazards included:

* Message corruption.
* Message loss.
* Message duplication.
* Message insertion.
* Incorrect sequencing.
* Excessive delay.
* Communication interruption.
* Masquerading.

The risk assessment demonstrated that several of these hazards possess High or Very High risk levels and therefore require dedicated safety mitigation measures [10].

---

### Safety Function Development

Six safety functions were derived to mitigate the identified communication hazards.

These functions included:

* Message integrity protection.
* Sequence supervision.
* Time supervision.
* Connection supervision.
* Endpoint validation.
* Safe-state activation.

Traceability was established between hazards, safety requirements, safety functions, and architectural elements in accordance with Functional Safety principles [1][4].

---

### Architectural Analysis

A RaSTA-based communication architecture was developed using the Black Channel Principle defined by EN 50159 [5].

The architecture demonstrated how safety can be achieved without requiring the communication network itself to be safety-certified. Instead, safety-related protection mechanisms are implemented at the communication endpoints through CRC verification, sequence monitoring, timeout supervision, and connection management functions [5][6].

---

### Quantitative Evaluation

The communication architecture was evaluated using several engineering methods.

The analyses included:

* Availability calculations.
* Reliability calculations.
* CRC residual error probability estimation.
* Reliability Block Diagram evaluation.
* Fault Tree Analysis.

The results demonstrated:

* Availability ≈ 99.99%
* Reliability ≈ 99.90%
* CRC residual error probability ≈ 2.33 × 10⁻¹⁰
* Hazardous communication event probability ≈ 2 × 10⁻¹¹

These results indicate that the communication system achieves a high level of dependability and safety [8][9].

---

## 8.3 Achievement of RAMS Objectives

The communication system was evaluated against the RAMS framework defined by EN 50126 [3].

### Reliability

The calculated reliability satisfies the reliability target established in Section 3.

### Availability

The calculated availability exceeds the required 99.99% target.

### Maintainability

Diagnostic logging, event recording, and fault reporting support maintenance and troubleshooting activities.

### Safety

The combination of CRC protection, sequence supervision, timing supervision, connection supervision, and safe-state activation significantly reduces the probability of hazardous communication failures.

The results demonstrate that the communication architecture supports all RAMS objectives defined for the project [3].

---

## 8.4 Functional Safety Perspective

From a Functional Safety perspective, the project demonstrates the application of several key safety engineering principles [1].

These principles include:

* Hazard-based design.
* Risk reduction through safety functions.
* Defense-in-depth.
* Fail-safe behaviour.
* Verification and validation.
* Traceability throughout the lifecycle.

The project also demonstrated how Functional Safety concepts can be applied to communication systems, where communication failures themselves become potential hazards requiring systematic risk control [1][4].

---

## 8.5 Final Conclusion

The analyses performed throughout this report demonstrate that RaSTA provides an effective framework for safety-related railway communication [6].

The protocol successfully addresses the communication hazards identified by EN 50159 through multiple independent safety mechanisms operating according to the Black Channel Principle [5]. These mechanisms provide robust protection against message corruption, loss, duplication, incorrect sequencing, excessive delay, communication interruption, and masquerading.

The quantitative analyses indicate that the probability of hazardous communication failures remains extremely low and satisfies typical railway safety expectations [4][9]. Furthermore, the architecture supports the achievement of the RAMS objectives defined by EN 50126 and the Functional Safety principles established by IEC 61508 [1][3].

Therefore, RaSTA can be considered a suitable and effective solution for safety-related communication in modern railway systems where safe transmission of information over non-safe communication channels is required.
