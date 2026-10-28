# CCSDS 524.2-B-1 & ISO 22646 Mission Operations MAL Binding to TCP/IP Specification

## Executive Summary

This document provides the technical architecture and wire protocol specification for **MALTCPForge Pro**, an industrial workstation implementation of the **Consultative Committee for Space Data Systems (CCSDS) 524.2-B-1** Recommended Standard (*Mission Operations—Message Abstraction Layer Binding to TCP/IP Transport and Split Binary Encoding*, Blue Book, Issue 1, November 2017\) and **ISO 22646**.

The CCSDS Message Abstraction Layer (MAL, CCSDS 521.0-B-2) provides high-level service interaction patterns across space agency ground infrastructures. CCSDS 524.2-B-1 specifies the exact on-the-wire framing, socket multiplexing, and Split Binary Encoding required to transport MAL service interactions over terrestrial and cross-agency IP networks (e.g. between ESA ESOC Darmstadt, NASA JPL Pasadena, DLR GSOC Oberpfaffenhofen, and commercial antenna stations).

---

## 1\. TCP/IP Wire Framing & Stream Preamble

\+-------------------------------------------------------------------------+

\|                  MAL-TCP Stream Preamble (9 Octets)                     \|

\|  \+-------------------------------------+--------+---------------------+  \|

\|  \|       Magic Delimiter (4 Octets)    \|Version \| Total Length (32b)  \|  \|

\|  \|       0x4D 0x41 0x4C 0x54 ("MALT")  \| (0x01) \| Big-Endian (Bytes)  \|  \|

\|  \+-------------------------------------+--------+---------------------+  \|

\+-------------------------------------------------------------------------+

\|                MAL Fixed Header (19-21 Octets)                          \|

\|  \+------------------------------------+-------------------------------+  \|

\|  \|    Service Area (16 bits)          \|    Service ID (16 bits)       \|  \|

\|  \+------------------------------------+-------------------------------+  \|

\|  \|     Operation ID (16 bits)         \|    Service Version (8 bits)   \|  \|

\|  \+--------------------+---------------+-------------------------------+  \|

\|  \| Pattern (8 bits)   \| Stage (8 bits)\| 64-Bit Transaction ID (64b)   \|  \|

\|  \+--------------------+---------------+-------------------------------+  \|

\|  \| Flags: Err(1b)\|HB(1b)\|QoS(3b)\|Sess(3b) \| Priority (8 bits)         \|  \|

\|  \+------------------------------------+-------------------------------+  \|

\|  \| \[Optional\] Error Code (16 bits if Err Flag \= 1\)                    \|  \|

\+-------------------------------------------------------------------------+

\|                MAL Variable Header (Length-Prefixed Strings)            \|

\|  \+--------------------------------------------------------------------+  \|

\|  \| URI From (16b len \+ UTF-8) • URI To (16b len \+ UTF-8)              \|  \|

\|  \| Domain (16b len \+ UTF-8)   • Network Zone (16b len \+ UTF-8)        \|  \|

\|  \| Session Name (16b len \+ UTF-8)                                     \|  \|

\+-------------------------------------------------------------------------+

\|               MAL Payload Body (Split Binary Encoding)                  \|

\|  \+------------------------------------+-------------------------------+  \|

\|  \|   Bit Stream Length (16 bits)      \| Bit Stream (Null & Booleans)  \|  \|

\|  \+------------------------------------+-------------------------------+  \|

\|  \|   Byte Stream (Numerical, Coordinates, UTF-8 Strings)              \|  \|

\+-------------------------------------------------------------------------+

### 1.1 TCP Byte-Stream Framing & Magic Delimiter

Because TCP/IP delivers an unsegmented stream of bytes rather than discrete packets, the standard prefixes each MAL interaction frame with:

- **Magic Delimiter**: 4 octets `0x4D 0x41 0x4C 0x54` (`ASCII: MALT`)  
- **Protocol Version**: 1 octet (`0x01`)  
- **Total Content Length**: 32-bit Big-Endian unsigned integer representing the byte length of the remaining header and payload fields.

---

## 2\. Split Binary Encoding Specification (§4)

In contrast to conventional binary serializers that interleave boolean flags with numerical fields, Split Binary Encoding segregates the message body into two continuous streams:

1. **Bit Stream**:  
   - Contains all 1-bit Null Presence Indicators (`0` \= null, `1` \= present).  
   - Contains all boolean attributes.  
   - Packed tightly into contiguous octets (MSB first, `bit 7` down to `bit 0`).  
2. **Byte Stream**:  
   - Contains integer, floating-point, coordinate ephemerides, timestamps, and string byte arrays.  
   - Non-null elements are serialized in pure sequence without alignment padding.

This architecture reduces wire overhead by 28–45% compared to XML or tagged JSON and allows zero-copy deserialization of telemetry vectors.