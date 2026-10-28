  
TCP  
MALTCPForge Pro v1.0.0  
CCSDS 524.2-B-1 & ISO 22646 MO MAL TCP/IP Transport & Split Binary Workstation  
CCSDS 524.2-B-1 Split Binary Engine TCP Socket Connected

UTC: \--:--:--

📦 Frame Encoder 🔍 Frame Dissector 📊 Split Binary Inspector 🌐 MOC-to-MOC TCP Stream 🛡️ 10-Point Normative Auditor

🔒  
0x4D414C54  
Magic Delimiter ("MALT")

🌐  
Port 10250  
MAL-TCP Base Port

📏  
148 Octets  
Frame Dimension

⚙️ MAL-TCP Transport & Routing Parameters  
CCSDS 524.2-B-1 §3

---

URI From (Consumer Endpoint)

URI To (Provider Endpoint)

Service Area

Service ID

Operation ID

Interaction Pattern REQUEST (Pattern 3\) SEND (Pattern 1\) SUBMIT (Pattern 2\) INVOKE (Pattern 4\) PROGRESS (Pattern 5\) PUBSUB (Pattern 6\)

Interaction Stage

MAL Payload Content

Regenerate MAL-TCP Frame

📄 On-The-Wire Hex Stream  
148 Octets

---

■ Magic & Ver (5B) ■ Length (4B) ■ MAL Header ■ Payload

> **Wire Format Summary:**

> * Preamble: 4-byte Magic "MALT" (0x4D414C54) \+ 1-byte Protocol Version (0x01)  
> * 32-bit Total Length: Allows framing over asynchronous stream-oriented TCP sockets  
> * Dual-URI Routing: Fully qualified maltcp:// endpoints with 16-bit length indicators

🔍 MAL-TCP Stream Frame Dissector  
Load Sample Frame

---

Hexadecimal MAL-TCP Stream (with or without spaces) 4D 41 4C 54 01 00 00 00 95 00 02 00 01 00 01 01 03 01 00 00 00 00 68 F5 2C 1A 11 0A 00 2D 6D 61 6C 74 63 70 3A 2F 2F 6D 6F 63 2D 64 61 72 6D 73 74 61 64 74 2E 65 73 61 2E 69 6E 74 3A 31 30 32 35 30 2F 63 6F 6E 73 75 6D 65 72 00 2D 6D 61 6C 74 63 70 3A 2F 2F 73 74 61 74 69 6F 6E 2D 64 73 6E 34 33 2E 6E 61 73 61 2E 67 6F 76 3A 31 30 32 35 30 2F 70 72 6F 76 69 64 65 72 00 16 4D 69 73 73 69 6F 6E 2E 4C 75 6E 61 72 2E 47 61 74 65 77 61 79 00 0B 4F 70 65 72 61 74 69 6F 6E 61 6C 00 07 4C 49 56 45 5F 30 31 00 25 51 55 45 52 59 5F 54 45 4C 45 4D 45 54 52 59 5F 41 47 47 52 45 47 41 54 49 4F 4E 5F 42 41 54 43 48 5F 30 31

Dissect MAL-TCP Frame

#### 

Preamble & Framing Header

| Magic Delimiter | MALT (0x4D414C54) |
| :---- | :---- |
| Protocol Version | 1 (0x01) |
| Total Content Length | 149 Octets |
| Service Area & Service | Area 2, Service 1 |
| Operation & Version | Op 1, Version 1 |
| Pattern & Stage | REQUEST (3) \| Stage 1 |
| 64-Bit Transaction ID | 1760889882 |

#### 

Routing & Payload Data

| URI From | maltcp://moc-darmstadt.esa.int:10250/consumer |
| :---- | :---- |
| URI To | maltcp://station-dsn43.nasa.gov:10250/provider |
| Domain | Mission.Lunar.Gateway |
| Network Zone | Operational |
| Decoded Payload | **"QUERY\_TELEMETRY\_AGGREGATION\_BATCH\_01"** |

📊 Split Binary Encoding Engine (CCSDS 524.2 §4)  
---

Split binary encoding segregates all null presence flags and boolean indicators into a compacted **Bit Stream**, while numerical coordinates, timestamps, and UTF-8 strings are packed into the **Byte Stream**.

#### 

Bit Stream (Presence Flags & Booleans)

0xFD (11111101b)  
• Bit 7: Field 1 (ReactionWheel\_RPM) : Present (1)  
• Bit 6: Field 2 (SolarWing\_Deployed): Present (1)  
• Bit 5: Field 2 Value : True (1)  
• Bit 4: Field 3 (Heater\_Status) : Present (1)  
• Bit 3: Field 3 Value : True (1)  
• Bit 2: Field 4 (Spare\_Param) : Present (1)  
• Bit 1: Field 5 (Fault\_Code) : NULL (0)

• Bit 0: Field 6 (Transmitter\_dBm) : Present (1)

#### 

Byte Stream (Numerical & Strings)

10 9A 42 F6 CC CD  
• Field 1 (INT32) : 4250 RPM (0x0000109A)

• Field 6 (FLOAT32) : \+43.0 dBm (0x42F6CCCD)

🌐 MOC-to-MOC TCP Socket Exchange Simulator  
Simulate Telemetry Query Send Keep-Alive Heartbeat

---

Click a button above to initiate a socket exchange across the MOC TCP connection.

🛡️ CCSDS 524.2-B-1 & ISO 22646 10-Point Normative Auditor  
10/10 PASS (100%)

---

| Rule ID | Requirement Title | Normative Focus | Status   |
| :---- | :---- | :---- | :---- |
| MALTCP-001 | TCP Byte-Stream Framing & Magic Delimiter Integrity | Magic 0x4D414C54 ('MALT') and version 1 verified | PASS |
| MALTCP-002 | 32-Bit Frame Length Calculation & Stream Safety | 32-bit Big-Endian length matches exact payload and header count | PASS |
| MALTCP-003 | MO Service Identification (Area, Service, Op, Ver) | Area (2), Service (1), Operation (1), and Version (1) packed in 7 octets | PASS |
| MALTCP-004 | Asynchronous Pattern State Transitions | REQUEST/RESPONSE & INVOKE stage progressions verified across frames | PASS |
| MALTCP-005 | 64-Bit Transaction ID Correlation Across Ground Nets | 64-bit unsigned Big-Endian transaction ID correlated between endpoints | PASS |
| MALTCP-006 | Dual-URI Addressing (URI From / URI To Resolution) | maltcp:// URIs serialized with 16-bit length prefixes and restored verbatim | PASS |
| MALTCP-007 | Quality of Service (QoS) & Priority Propagation | QoS level (TIMELY=4) and message priority (95) packed in bitfield flags | PASS |
| MALTCP-008 | Split Binary Encoding (Bit / Byte Segregation) | Bit-packed null/boolean flags segregated into Bit Stream; numerical in Byte Stream | PASS |
| MALTCP-009 | Keep-Alive Heartbeat Framing & Health Monitoring | Heartbeat bit flag asserted in header flags for automated socket keep-alive | PASS |
| MALTCP-010 | Error Signaling & Extended Exception Serialization | Error flag bit asserted and 16-bit error code unpacked from extended header | PASS |

