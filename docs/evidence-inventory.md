# Evidence Inventory

## Investigation: Suspicious Network Activity

### 1. Primary Evidence — C2 Beaconing

- File: `captures/01-c2-beaconing.pcap`
- Source: https://cyber.encse.com/labs/network-forensics/01-c2-beaconing.pcap
- Source type: Synthetic cybersecurity training lab
- Format: PCAP 2.4
- Encapsulation: Ethernet
- Packets: 104
- Captured bytes: 6,316
- Duration: 799.425975 seconds
- SHA-256: `f0c23c3a6b7f897fb5ee103da19580e49b12ab311eefe9e8ad5311f2ab068ae3`

### 2. Reference Evidence — Clean Baseline

- File: `captures/04-baseline-clean.pcap`
- Source: https://cyber.encse.com/labs/network-forensics/04-baseline-clean.pcap
- Source type: Synthetic cybersecurity training lab
- Format: PCAP 2.4
- Encapsulation: Ethernet
- Packets: 25
- Captured bytes: 1,721
- Duration: 241.565646 seconds
- SHA-256: `d9f2f345fd3d27532abee88accdd364ece535ac0958670c0dd9599eb29843069`

### 3. Previous Project Baseline

- File: `captures/baseline.pcapng`
- Source: Locally generated during Project 2, Phase 2
- Purpose: Reference for previously observed network traffic
- Format: PCAPNG

### 4. Integrity and Handling

- SHA-256 checksums were calculated after download.
- TShark successfully processed both laboratory captures.
- Capture files remain excluded from Git through `.gitignore`.
- No capture files are included in the repository.

### 5. Initial Limitations

- The laboratory captures are synthetic training evidence, not recordings of a confirmed real-world incident.
- The timestamps require careful interpretation; the displayed dates precede the Unix epoch.
- The calculated hashes identify the downloaded files but have not been independently verified against publisher-provided checksums.
- No malicious activity has been confirmed at this stage.

