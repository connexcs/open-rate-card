# Telecommunications Glossary

A comprehensive reference of telecommunications and VoIP terminology used in the Open Rate Card specification.

---

## A

### ACD (Average Call Duration)
**Definition:** The average length of completed calls in seconds.  
**Typical Range:** 60-300 seconds for quality traffic  
**Usage:** Quality metric indicating genuine call traffic vs. testing or fraud  
**Example:** `"acd": 60` means average call is 60 seconds

### ANI (Automatic Number Identification)
**Definition:** The telephone number of the calling party, automatically transmitted with the call.  
**Related:** CLI (Calling Line Identification)  
**Usage:** Used for billing, routing, and caller identification  
**Note:** In SIP, transmitted in the From header or P-Asserted-Identity

### ASR (Answer Seizure Ratio)
**Definition:** Percentage of call attempts that successfully connect (answer).  
**Formula:** (Answered Calls / Total Attempts) × 100  
**Target:** >50% for quality routes, >70% for premium  
**Usage:** Key quality indicator for route performance  
**Example:** `"asr": 90` means 90% of calls successfully connect

---

## C

### CDR (Call Detail Record)
**Definition:** Detailed record of each call including time, duration, parties, and billing information.  
**Contains:** Timestamp, caller, called number, duration, disposition, cost  
**Formats:** CSV, JSON, XML, proprietary  
**Usage:** Billing, traffic analysis, fraud detection

### CLI (Calling Line Identification)
**Definition:** The displayed phone number of the caller.  
**Related:** ANI, CLID  
**Standards:** E.164 format recommended  
**Privacy:** Can be withheld or restricted  
**SIP Header:** From, P-Asserted-Identity

### CNAM (Caller Name)
**Definition:** Database lookup service that provides the name associated with a phone number.  
**Usage:** Displays "John Doe" or "ABC Company" instead of just phone number  
**Implementation:** DIP (database dip) query during call setup  
**Cost:** Typically per-query charge  
**Example:** `"cnam_lookup": true`

### Codec
**Definition:** Algorithm for encoding/decoding audio (COder-DECoder).  
**Common Types:**
- **G.711 (PCMU/PCMA)**: 64 kbps, highest quality, no compression
- **G.729**: 8 kbps, compressed, requires licensing
- **G.722**: Wideband, 64 kbps
- **OPUS**: Modern, adaptive bitrate (6-510 kbps)
- **iLBC**: 15.2 kbps, good for packet loss
- **AMR/AMR-WB**: Mobile optimized

**Trade-off:** Bandwidth vs. quality vs. CPU usage

### CPS (Calls Per Second)
**Definition:** The number of new call setups that can be processed per second.  
**Typical Range:** 1-100 CPS depending on capacity  
**Usage:** Capacity planning and load testing  
**Example:** `"cps": 10` means 10 new calls can be initiated each second

---

## D

### DID (Direct Inward Dialing)
**Definition:** Phone numbers that connect directly to a specific extension without going through an operator.  
**Also Known As:** DDI (Direct Dialing In) in Europe  
**Usage:** Allow external callers to reach specific users directly  
**Example:** Company has 555-1000 to 555-1999 as DID range

### DTMF (Dual-Tone Multi-Frequency)
**Definition:** Touch-tone signaling system for transmitting digits during a call.  
**Methods:**
- **RFC 2833**: Out-of-band (recommended)
- **In-band**: Audio tones
- **SIP INFO**: SIP message method

**Usage:** IVR navigation, conference codes, PIN entry  
**Example:** `"dtmf_mode": "rfc2833"`

---

## E

### E.164
**Definition:** ITU-T international public telecommunication numbering plan.  
**Format:** `[+][country code][national number]`  
**Max Length:** 15 digits  
**Example:** `+442012345678` (UK), `+12125551234` (USA)  
**Usage:** Standard format for international phone numbers

### E911
**Definition:** Enhanced 911 - Emergency calling system in the US that provides location information.  
**Requirements:** FCC mandated for VoIP providers  
**Related:** E112 (Europe), 000 (Australia)  
**Usage:** Emergency services routing and location  
**Example:** `"e911_supported": true`

---

## F

### Failover
**Definition:** Automatic switching to backup systems when primary fails.  
**Methods:**
- **Sequential**: Try endpoints in priority order
- **Parallel**: Attempt multiple simultaneously

**Configuration:** Retry codes, timeouts, priority levels  
**Example:** Primary endpoint fails, calls route to backup

---

## H

### H.323
**Definition:** Legacy VoIP protocol suite (predecessor to SIP).  
**Status:** Declining use, replaced by SIP  
**Components:** H.225, H.245, RTP  
**Usage:** Legacy enterprise systems

---

## I

### IAX2 (Inter-Asterisk eXchange)
**Definition:** VoIP protocol designed for server-to-server communication.  
**Port:** UDP 4569  
**Advantages:** NAT-friendly, efficient trunking  
**Usage:** Primarily with Asterisk PBX systems

### ICE (Interactive Connectivity Establishment)
**Definition:** NAT traversal technique combining STUN and TURN.  
**Standard:** RFC 8445  
**Usage:** WebRTC, modern SIP implementations  
**Purpose:** Establish optimal media path through NAT/firewalls

---

## J

### Jitter
**Definition:** Variation in packet arrival times, measured in milliseconds.  
**Acceptable:** <30ms  
**Impact:** Causes choppy audio, gaps in conversation  
**Solution:** Jitter buffer to smooth delivery  
**Example:** `"jitter": 30` means 30ms variation acceptable

### Jitter Buffer
**Definition:** Buffer that smooths out packet arrival time variations.  
**Types:** Fixed, adaptive  
**Trade-off:** Larger buffer = smoother audio but higher latency  
**Configuration:** Min/max buffer size

---

## L

### Latency
**Definition:** Time delay for packets to travel from source to destination.  
**One-Way Acceptable:** <150ms  
**Round-Trip Acceptable:** <300ms  
**Impact:** Causes conversation overlap, echo perception  
**Example:** `"latency": 150` means 150ms one-way delay

### LNP (Local Number Portability)
**Definition:** Ability to keep your phone number when switching carriers.  
**Process:** LNP database lookup (DIP) to determine current carrier  
**Cost:** Per-query charge  
**Requirement:** FCC mandated in US  
**Example:** `"lnp": { "enabled": true, "dip_on_all_calls": true }`

---

## M

### MCC (Mobile Country Code)
**Definition:** Three-digit code identifying a mobile network's country.  
**Example:** `234` = United Kingdom, `310` = United States  
**Usage:** Mobile routing, SMS delivery  
**Standard:** ITU-T E.212

### MNC (Mobile Network Code)
**Definition:** Two or three-digit code identifying mobile carrier within a country.  
**Example:** `234-10` = O2 UK, `310-410` = AT&T USA  
**Usage:** Mobile termination routing  
**Combination:** MCC+MNC uniquely identifies carrier

### MOS (Mean Opinion Score)
**Definition:** Numerical measure of voice call quality.  
**Scale:** 1.0 (bad) to 5.0 (excellent)  
**Typical Range:**
- **4.3-5.0**: Excellent (toll quality)
- **4.0-4.3**: Good
- **3.6-4.0**: Fair
- **3.1-3.6**: Poor
- **1.0-3.1**: Bad

**Calculation:** Based on R-factor from E-model algorithm  
**Usage:** SLA requirements, quality monitoring  
**Example:** `"mos_min": 3.5` requires minimum 3.5 MOS score

---

## N

### NAT (Network Address Translation)
**Definition:** Technique for mapping private IP addresses to public IPs.  
**Challenge:** Breaks direct media paths in VoIP  
**Solutions:**
- **STUN**: Discover public IP/port
- **TURN**: Relay through server
- **ICE**: Combination strategy
- **ALG**: Application Layer Gateway (router feature)

**Example:** `"nat": { "enabled": true, "stun_server": "stun.provider.com" }`

### NER (Network Effectiveness Ratio)
**Definition:** Percentage of calls successfully routed and delivered.  
**Formula:** (Successfully Completed Calls / Total Attempts) × 100  
**Target:** 95-100%  
**Includes:** Routing failures, network issues  
**Example:** `"ner": 100` means 100% routing success

---

## O

### OPUS
**Definition:** Modern audio codec with variable bitrate.  
**Bitrate Range:** 6-510 kbps  
**Advantages:** Adaptive quality, excellent for varying network conditions  
**Usage:** WebRTC, modern VoIP systems  
**Standard:** RFC 6716

---

## P

### P-Asserted-Identity (PAI)
**Definition:** SIP header containing authenticated caller identity.  
**Usage:** Trusted networks, caller verification  
**Privacy:** Can be withheld from end users  
**Example:** `"pre_assert_id": "required"`

### Packet Loss
**Definition:** Percentage of packets that fail to reach destination.  
**Acceptable:** <1%  
**Impact:** Audio dropouts, quality degradation  
**Causes:** Network congestion, errors  
**Mitigation:** FEC (Forward Error Correction), PLC (Packet Loss Concealment)  
**Example:** `"packet_loss": 1` allows up to 1% loss

### PDD (Post Dial Delay)
**Definition:** Time from last digit dialed until ring tone or progress indication.  
**Acceptable:** <3 seconds  
**Impact:** User experience, perceived call setup speed  
**Includes:** Routing lookup, carrier connection  
**Example:** `"pdd": 3` means maximum 3 seconds to ring

---

## R

### RTCP (RTP Control Protocol)
**Definition:** Control protocol for RTP providing statistics and quality monitoring.  
**Information:** Packet counts, jitter, loss, round-trip time  
**Usage:** Quality monitoring, adaptive bitrate  
**Standard:** RFC 3550

### RTP (Real-time Transport Protocol)
**Definition:** Protocol for delivering audio and video over IP networks.  
**Port Range:** Typically 10000-20000 (configurable)  
**Features:** Sequence numbers, timestamps, payload type  
**Usage:** Media stream delivery  
**Standard:** RFC 3550

---

## S

### SIP (Session Initiation Protocol)
**Definition:** Signaling protocol for initiating, maintaining, and terminating calls.  
**Port:** 5060 (UDP/TCP), 5061 (TLS)  
**Methods:** INVITE, ACK, BYE, CANCEL, REGISTER, OPTIONS  
**Standard:** RFC 3261  
**Usage:** Most common VoIP signaling protocol

### SMS (Short Message Service)
**Definition:** Text messaging service for mobile phones.  
**Length:**
- **GSM-7**: 160 characters per segment
- **UCS-2/Unicode**: 70 characters per segment

**Concatenation:** Multi-segment messages for longer texts  
**DLR**: Delivery receipt confirmation

### SRTP (Secure Real-time Transport Protocol)
**Definition:** Encrypted version of RTP for secure media transport.  
**Encryption:** AES-128 or AES-256  
**Key Exchange:** SDES, DTLS-SRTP  
**Usage:** Secure/private communications  
**Standard:** RFC 3711

### STIR/SHAKEN
**Definition:** Standards for authenticating caller ID to prevent spoofing.  
**STIR:** Secure Telephone Identity Revisited (RFC 8224)  
**SHAKEN:** Signature-based Handling of Asserted information using toKENs  
**Attestation Levels:**
- **A (Full)**: Provider verified call origin and caller ID
- **B (Partial)**: Provider verified call origin but not caller ID
- **C (Gateway)**: Call entered network via gateway

**Requirement:** FCC mandated for US carriers  
**Usage:** Fraud prevention, robocall mitigation  
**Example:** `"stir_shaken_attestation": "A"`

### STUN (Session Traversal Utilities for NAT)
**Definition:** Protocol for discovering public IP address and NAT type.  
**Port:** 3478 (UDP/TCP)  
**Usage:** NAT traversal, ICE  
**Standard:** RFC 5389  
**Example:** `"stun_server": "stun.provider.com"`

---

## T

### T.38
**Definition:** ITU-T protocol for fax over IP networks.  
**Alternative:** Fax passthrough (less reliable)  
**Advantages:** More reliable than audio fax  
**Usage:** Fax transmission over VoIP  
**Example:** `"t38_enabled": true`

### TLS (Transport Layer Security)
**Definition:** Encryption protocol for securing SIP signaling.  
**Port:** 5061  
**Versions:** TLS 1.2, TLS 1.3 (recommended)  
**Usage:** Secure SIP (SIPS)  
**Example:** `"transport": "tls"`

### TURN (Traversal Using Relays around NAT)
**Definition:** Relay protocol for media when direct connection fails.  
**Port:** 3478  
**Usage:** Fallback for difficult NAT situations  
**Bandwidth:** Higher server bandwidth usage  
**Standard:** RFC 5766

---

## U

### Uptime
**Definition:** Percentage of time service is available and operational.  
**Calculation:** (Total Time - Downtime) / Total Time × 100  
**Common SLAs:**
- **99.9% ("three nines")**: 8.76 hours downtime/year
- **99.99% ("four nines")**: 52.6 minutes downtime/year
- **99.999% ("five nines")**: 5.26 minutes downtime/year

**Example:** `"uptime": 99.9`

---

## V

### VoIP (Voice over IP)
**Definition:** Technology for voice communications over internet protocol networks.  
**Protocols:** SIP, H.323, IAX2  
**Advantages:** Cost savings, flexibility, feature rich  
**Components:** Signaling, media (RTP), codecs

---

## W

### WebRTC
**Definition:** Browser-based real-time communication technology.  
**Features:** Audio, video, data channels  
**Security:** Mandatory encryption (SRTP, DTLS)  
**NAT:** Built-in ICE support  
**Usage:** Browser calling, video conferencing

### Wholesale
**Definition:** Business model where carriers sell services to other carriers/resellers.  
**Pricing:** Lower rates, higher volumes  
**Typical:** Per-minute or per-second billing  
**Contrast:** Retail (direct to end users)

---

## Standards References

- **E.164**: International numbering plan (ITU-T)
- **E.212**: Mobile network codes (ITU-T)
- **ISO 3166-1**: Country codes
- **ISO 4217**: Currency codes
- **ISO 8601**: Date and time formats
- **RFC 2833**: DTMF over RTP
- **RFC 3261**: SIP protocol
- **RFC 3550**: RTP/RTCP
- **RFC 3711**: SRTP
- **RFC 5389**: STUN
- **RFC 5766**: TURN
- **RFC 6716**: OPUS codec
- **RFC 8224**: STIR framework
- **RFC 8445**: ICE

---

## See Also

- [SPECIFICATION.md](SPECIFICATION.md) - Complete field reference
- [CARD-TYPES.md](CARD-TYPES.md) - Rate card types
- [README.md](README.md) - Project overview

---

**Document Version**: 1.0.0  
**Last Updated**: January 3, 2026  
**License**: MIT
