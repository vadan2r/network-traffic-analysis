# Technical Report — Suspicious Network Activity Investigation

## 1. Executive Summary

This investigation examined a synthetic network traffic capture containing repeated outbound connections from an internal host to a single destination address.

The analysis identified 14 TCP sessions from `10.0.5.23` to `203.0.113.77:443`, occurring at approximately one-minute intervals. The sessions exhibited consistent packet counts, byte volumes, durations, and request-response characteristics.

The observed behavior is consistent with automated periodic communication and has characteristics commonly associated with network beaconing.

However, the available evidence does not establish malicious intent, malware execution, data exfiltration, command-and-control activity, or host compromise.

The investigation also identified six isolated outbound TCP SYN attempts to different destinations. Their relationship to the primary communication pattern could not be established.

Both laboratory captures are synthetic training datasets. The findings must therefore be interpreted as the results of a synthetic training exercise, not as evidence of a real-world security incident.

## 2. Investigation Scope

### 2.1 Objective

The objective was to examine potentially suspicious network activity, identify recurring communication patterns, correlate relevant evidence, evaluate alternative explanations, and document findings with appropriate confidence levels.

### 2.2 Evidence Sources

The investigation used two synthetic laboratory captures:

**Primary evidence**
- File: `captures/01-c2-beaconing.pcap`
- Packets: 104
- Captured bytes: 6,316
- Duration: 799.425975 seconds
- SHA-256: `f0c23c3a6b7f897fb5ee103da19580e49b12ab311eefe9e8ad5311f2ab068ae3`

**Reference evidence**
- File: `captures/04-baseline-clean.pcap`
- Packets: 25
- Captured bytes: 1,721
- Duration: 241.565646 seconds
- SHA-256: `d9f2f345fd3d27532abee88accdd364ece535ac0958670c0dd9599eb29843069`

Both files were obtained from the same cybersecurity training laboratory.

A separate locally generated capture, `captures/baseline.pcapng`, was previously analyzed during Phase 2 to document ordinary workstation network activity. It provided additional learning context but was not used as a controlled baseline for the laboratory investigation.

## 3. Methodology and Tools

The investigation followed a structured network traffic analysis workflow:

1. Evidence identification and integrity documentation.
2. Initial traffic triage and protocol inventory.
3. Endpoint, TCP conversation, and connection-pattern analysis.
4. Payload inspection and TCP stream reconstruction.
5. Statistical analysis of session intervals.
6. Comparison with the supplied reference capture.
7. Evidence correlation and alternative hypothesis assessment.

Tools used:
- Wireshark and TShark 4.6.4
- Python 3 for automated extraction, comparison, and statistical calculations
- Git and GitHub for version control and documentation

TShark display filters, conversation statistics, protocol hierarchy analysis, and TCP stream reconstruction were used to examine the captures.

Python automation was used to compare payload characteristics across the 14 primary sessions and calculate interval statistics.

## 4. Key Findings

### 4.1 Repeated Outbound TCP Sessions

The primary capture contains 14 TCP sessions between:

- Source: `10.0.5.23`
- Destination: `203.0.113.77`
- Destination port: `443`

Each session exhibited:
- 7 frames
- 428 bytes
- Approximately 0.17 seconds of duration
- A consistent bidirectional exchange

The sessions were successfully established and terminated through observed TCP handshakes and FIN exchanges.

### 4.2 Periodic Communication

The 14 session start times produced 13 intervals:

| Metric | Result |
|---|---:|
| Mean interval | 60.175 seconds |
| Minimum interval | 58.002 seconds |
| Maximum interval | 61.984 seconds |
| Standard deviation | 1.386 seconds |
| Coefficient of variation | 2.30% |

The low relative variation supports the interpretation of automated periodic communication.

This timing pattern is consistent with beaconing behavior, although periodic communication can also occur in legitimate applications and services.

### 4.3 Payload Characteristics

Each primary session contained:
- One 29-byte client payload.
- One 21-byte server payload.

Across the 14 sessions:
- All client payloads were different.
- All server payloads were identical.

The client payloads contained 24 printable Base64-character sequences after the observed five-byte TLS-like record header. Decoding these sequences produced 18-byte binary values.

The server response was identical in all sessions and contained the ASCII characters `OK` followed by null bytes.

These characteristics establish a repeated request-response pattern, but the available data does not reveal the application-level purpose of the messages.

### 4.4 Protocol Characteristics

The payloads contained TLS-like record headers.

However, the client record declared a 64-byte content length while only 24 bytes following its header were present in the observed segment. The server record declared 16 bytes and contained that amount of data.

A conventional TLS handshake and negotiated application protocol were not established.

Consequently, the traffic should not be described as confirmed standard TLS communication based solely on these observations.

### 4.5 Isolated Connection Attempts

Six additional outbound TCP SYN packets were identified from `10.0.5.23` to six different IP addresses on destination port 443.

Each attempt appeared as a single SYN packet, with no corresponding response observed in the capture.

These events were treated separately from the 14 periodic sessions because no evidence established a common process, purpose, or causal relationship.

## 5. Reference Capture Comparison

The supplied `04-baseline-clean.pcap` contained:

- Five TCP conversations to different destinations.
- Three frames and 162 bytes per TCP conversation.
- Approximately 0.04 seconds per conversation.
- DNS traffic over UDP.
- No TCP payload segments observed.

In contrast, the primary capture contained 14 repeated sessions to one destination, with consistent bidirectional payload exchanges and approximately one-minute intervals.

This comparison demonstrates a behavioral difference between the two synthetic datasets.

It does not establish that every deviation from the reference is malicious, nor does it constitute a controlled statistical comparison.

## 6. Evidence Correlation

The following findings reinforce one another:

- Repeated connections to the same destination.
- Consistent session structure and duration.
- Approximately one-minute intervals with low relative variation.
- Variable client payloads and identical server responses.

Together, these observations support the interpretation of automated periodic communication.

The six isolated SYN attempts share the same source host but lack sufficient evidence to associate them with the primary communication pattern.

The payload and protocol characteristics remain insufficient to identify the application or determine the purpose of the communication.

## 7. Alternative Explanations

The observed pattern may be explained by:

1. A legitimate monitoring or telemetry agent communicating periodically.
2. A proprietary client using a fixed request-response protocol.
3. A synthetic communication pattern designed for the training scenario.
4. Malicious automated communication exhibiting beaconing characteristics.

The available evidence does not conclusively distinguish between these possibilities.

## 8. Confidence Assessment

| Assessment | Confidence | Supporting Evidence |
|---|---|---|
| Repeated periodic communication exists | High | Fourteen sessions and measured intervals. |
| The primary communication is automated | High | Consistent timing and session structure. |
| The behavior is consistent with beaconing | Moderate to high | Repeated destination, timing regularity, and recurring exchanges. |
| The isolated SYN attempts are related to the primary pattern | Undetermined | Only the source host is shared. |
| The communication is malicious | Undetermined | No sufficient evidence of malicious intent or compromise. |

## 9. Limitations

- Both laboratory captures are synthetic training datasets.
- `203.0.113.77` belongs to the TEST-NET-3 documentation range and does not represent evidence of a real command-and-control server.
- The payload contents do not reveal the application-level purpose of the communication.
- A conventional TLS handshake was not demonstrated.
- No endpoint process telemetry was available to identify the application responsible for the connections.
- No corresponding responses to the six isolated SYN attempts were observed, but this does not prove that the destinations were unavailable.
- The supplied baseline is a reference dataset, not a controlled experiment.
- The timestamps displayed by capture metadata require careful interpretation because they precede the Unix epoch.
- SHA-256 hashes were calculated locally but were not independently verified against publisher-provided checksums.

## 10. Final Conclusion

The investigation identified a repeated, approximately one-minute outbound communication pattern between `10.0.5.23` and `203.0.113.77:443`.

The consistency of session timing, packet structure, and request-response characteristics strongly supports the interpretation of automated periodic communication. The behavior is consistent with network beaconing.

Nevertheless, the available evidence does not establish malicious activity, malware execution, command-and-control operations, data exfiltration, or host compromise.

The six isolated outbound SYN attempts remain separate events without a demonstrated relationship to the primary communication pattern.

The appropriate conclusion is that the capture exhibits suspicious-looking automated periodic communication. If similar behavior were observed in a real-world environment, it would warrant further investigation. However, the evidence available in this synthetic dataset is insufficient to confirm malicious activity.

Further attribution would require endpoint process telemetry, application context, and a more complete protocol-level capture.

---

## 11. Supporting Documentation

- `docs/evidence-inventory.md` — Evidence sources, metadata, hashes, and handling.
- `docs/baseline-analysis.md` — Previous workstation network traffic baseline.
- `docs/evidence-correlation.md` — Correlation matrix, alternative explanations, and confidence assessment.
