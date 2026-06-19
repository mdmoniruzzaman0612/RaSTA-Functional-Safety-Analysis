# 1. Executive Summary

Railway signalling systems require communication mechanisms that guarantee both safety and availability. Communication failures such as message corruption, repetition, deletion, insertion, resequencing, and excessive transmission delay can result in hazardous system behaviour. To address these challenges, the railway industry employs specialised safety communication protocols designed in accordance with the requirements of DIN EN 50159.

This report presents a functional safety analysis of the Rail Safe Transport Application (RaSTA) protocol specified in DIN VDE V 0831-200 Version 03.03 [1]. RaSTA is a safety communication protocol developed for railway signalling applications to provide secure and highly available data transmission over standard communication networks. The protocol is independent of the application layer and can operate over conventional transport services such as UDP and TCP [1].

RaSTA achieves safety through a Safety and Retransmission Layer that provides message integrity verification, sequence supervision, adaptive timeliness monitoring, connection supervision, and retransmission of lost data. Availability is improved through a Redundancy Layer that supports multiple transport channels and allows communication to continue despite failures in individual channels [1].

The protocol employs sequence numbers, confirmed sequence numbers, timestamps, confirmed timestamps, heartbeat messages, retransmission requests, retransmission responses, and cryptographic safety codes based on MD4 to detect communication errors and maintain compliance with railway safety communication requirements [1]. Additional protection is provided by redundancy-layer cyclic redundancy checks (CRC) and duplicate elimination mechanisms [1].

This report evaluates the architecture, communication mechanisms, safety functions, and operational behaviour of RaSTA. The protocol's connection establishment procedures, state transitions, retransmission mechanisms, adaptive channel monitoring functions, and redundancy management techniques are analysed to demonstrate compliance with the safety objectives of railway communication systems.

The analysis concludes that RaSTA Version 03.03 provides a structured and standards-based approach to achieving safe and highly available communication for railway signalling applications through the combined use of integrity protection, sequence supervision, timeliness monitoring, retransmission, and communication redundancy [1].
