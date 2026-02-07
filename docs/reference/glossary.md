# Glossary

## A

**Access Control**
- Security measures that restrict who can access physical or digital resources. In BLE context, often refers to smart locks and building access systems.

**Advertising**
- BLE mechanism where devices broadcast their presence and capabilities. Advertising packets contain device name, services, and other metadata.

**AES (Advanced Encryption Standard)**
- Symmetric encryption algorithm used in BLE security. BLE uses AES-128 CCM mode.

**Android HID**
- Android API (Android 9+) that allows apps to emulate HID devices like keyboards and mice over Bluetooth.

**ATT (Attribute Protocol)**
- Protocol in BLE stack that defines how data is structured and accessed. Foundation for GATT.

**Attack Surface**
- All possible points where an attacker could exploit a system. In BLE, includes physical layer, link layer, GATT, and application.

## B

**Baseband**
- Lower layer of Bluetooth protocol stack responsible for physical radio communication.

**Beacon**
- BLE device that transmits advertising packets without accepting connections. Used for proximity detection (e.g., iBeacon).

**BLE (Bluetooth Low Energy)**
- Low-power wireless protocol introduced in Bluetooth 4.0. Designed for IoT, wearables, and battery-powered devices.

**Bonding**
- Process of storing pairing keys for future connections, allowing devices to reconnect without re-pairing.

**Brute Force**
- Attack method that tries all possible combinations to crack passwords, PINs, or encryption keys.

**BTHCI (Bluetooth Host Controller Interface)**
- Interface between Bluetooth host and controller. Useful for packet capture and analysis.

**btlejack**
- Tool for BLE MITM attacks, packet sniffing, and connection following.

## C

**CCM (Counter with CBC-MAC)**
- Authenticated encryption mode used in BLE. Provides both confidentiality and authenticity.

**Central**
- BLE role that initiates connections. Typically a smartphone or computer.

**Characteristic**
- Data element in GATT hierarchy. Contains a value and properties (read, write, notify, etc.).

**Challenge-Response**
- Authentication method where server sends random challenge, client proves identity by correctly responding.

**Connection Interval**
- Time between two consecutive connection events in BLE. Affects power consumption and latency.

**CRC (Cyclic Redundancy Check)**
- Error-detection code used in BLE packets.

## D

**Descriptor**
- Metadata associated with a characteristic. Examples: Client Characteristic Configuration Descriptor (CCCD), User Description.

**Denial of Service (DoS)**
- Attack that makes system unavailable. In BLE: connection flooding, jamming, battery drain.

**Downgrade Attack**
- Forcing system to use older, less secure protocol version or security mechanism.

## E

**ECDH (Elliptic Curve Diffie-Hellman)**
- Key exchange algorithm used in BLE Secure Connections (4.2+).

**EDR (Endpoint Detection and Response)**
- Security solution that monitors endpoints for malicious activity. Can detect HID injection attacks.

**Enumeration**
- Process of discovering and cataloging services, characteristics, and properties of BLE device.

**Exfiltration**
- Unauthorized transfer of data from system. Can be done via HID injection commands or RFCOMM.

## F

**Fail-Safe**
- Security design that defaults to permissive state on failure (e.g., unlock on power loss). Prioritizes safety.

**Fail-Secure**
- Security design that defaults to restrictive state on failure (e.g., remain locked on power loss). Prioritizes security.

**Firmware**
- Software embedded in hardware device. In BLE context, runs on microcontroller and implements protocol stack.

**Fuzzing**
- Automated testing technique that sends malformed or unexpected input to find vulnerabilities.

## G

**GATT (Generic Attribute Profile)**
- BLE profile that defines hierarchical data structure (services, characteristics, descriptors) and how to access it.

**GAP (Generic Access Profile)**
- BLE profile that defines device roles (central, peripheral), discovery, and connection procedures.

## H

**Handle**
- Unique identifier for attributes in GATT. Used to read/write specific characteristics.

**HCI (Host Controller Interface)**
- See BTHCI.

**HID (Human Interface Device)**
- USB and Bluetooth profile for input devices (keyboards, mice, game controllers).

**HID Injection**
- Attack technique where attacker emulates keyboard to inject keystrokes into target computer.

**HMAC (Hash-based Message Authentication Code)**
- Cryptographic hash used for message authentication.

## I

**IRK (Identity Resolving Key)**
- Key used to resolve resolvable private addresses in BLE.

**IDS/IPS (Intrusion Detection/Prevention System)**
- Security system that monitors network traffic for malicious activity.

## J

**Jamming**
- Intentional radio interference to prevent wireless communication.

**Just Works**
- BLE pairing method with no user interaction. Provides no MITM protection.

## K

**Key Extraction**
- Recovering cryptographic keys from device through side-channel attacks, reverse engineering, or vulnerabilities.

**Keystroke Injection**
- See HID Injection.

## L

**L2CAP (Logical Link Control and Adaptation Protocol)**
- BLE layer that handles packet segmentation and reassembly.

**LE (Low Energy)**
- Refers to Bluetooth Low Energy as opposed to Classic Bluetooth.

**Link Layer**
- BLE layer responsible for advertising, scanning, and connection management.

**LOLBins (Living Off the Land Binaries)**
- Legitimate system tools (PowerShell, cmd, certutil) used maliciously to avoid detection.

## M

**MAC Address**
- Media Access Control address. Unique identifier for Bluetooth devices. Can be static or random.

**Man-in-the-Middle (MITM)**
- Attack where attacker intercepts and potentially modifies communication between two parties.

**MFA (Multi-Factor Authentication)**
- Security mechanism requiring multiple forms of verification (e.g., password + SMS code).

**Modifier Keys**
- Keyboard keys like Ctrl, Shift, Alt, GUI (Windows key) used in combinations.

## N

**nRF Connect**
- Nordic Semiconductor's mobile app for BLE scanning and GATT exploration.

**Nonce**
- Number used once in cryptographic protocols. Prevents replay attacks.

**Numeric Comparison**
- BLE pairing method where users compare 6-digit numbers on both devices. Provides MITM protection.

## O

**OOB (Out of Band)**
- Pairing method using secondary channel (NFC, QR code) to exchange keys.

**OSCP (Offensive Security Certified Professional)**
- Penetration testing certification.

## P

**Pairing**
- Process of establishing shared keys between BLE devices for secure communication.

**Passkey Entry**
- BLE pairing method where user enters PIN on one or both devices.

**Payload**
- Malicious code or commands delivered via attack (e.g., HID injection payload).

**Peripheral**
- BLE role that advertises and accepts connections. Typically a sensor or IoT device.

**Persistence**
- Maintaining access to compromised system across reboots. Often via scheduled tasks or startup items.

**PHY (Physical Layer)**
- Lowest layer of protocol stack. Handles radio transmission.

**PIN**
- Personal Identification Number used in authentication.

**Privilege Escalation**
- Gaining higher access level than originally granted (e.g., user to administrator).

## R

**Reconnaissance**
- Information gathering phase of attack. In BLE: scanning, enumeration, traffic analysis.

**Replay Attack**
- Capturing and retransmitting valid communication to gain unauthorized access.

**Resolvable Private Address (RPA)**
- BLE MAC address that periodically changes to prevent tracking. Can be resolved with IRK.

**RFCOMM**
- Bluetooth profile that emulates serial port. Used in HID Scanner's SPP mode.

**RSSI (Received Signal Strength Indicator)**
- Measurement of radio signal power. Used to estimate distance.

## S

**Secure Connections**
- Enhanced security in BLE 4.2+ using ECDH and numeric comparison. Provides MITM protection.

**Secure Element**
- Hardware component designed for secure key storage and cryptographic operations.

**Security Manager Protocol (SMP)**
- BLE protocol responsible for pairing, bonding, and key distribution.

**Service**
- Grouping of related characteristics in GATT hierarchy.

**Side-Channel Attack**
- Exploiting information leaked through implementation (power consumption, timing, EM radiation).

**SIM Swapping**
- Attack where attacker convinces carrier to transfer phone number to their SIM card, bypassing SMS-based MFA.

**Sniffing**
- Capturing and analyzing network traffic. In BLE: using Ubertooth or nRF Sniffer.

**SPP (Serial Port Profile)**
- Bluetooth profile for serial communication. See RFCOMM.

**SQL Injection**
- Exploiting database queries by injecting malicious SQL code.

## T

**TOCTOU (Time of Check to Time of Use)**
- Race condition vulnerability where state changes between security check and resource use.

**TOTP (Time-based One-Time Password)**
- MFA method generating codes based on current time. More secure than SMS.

## U

**Ubertooth**
- Open-source BLE/Bluetooth sniffer hardware.

**UUID (Universally Unique Identifier)**
- 128-bit identifier for BLE services and characteristics. Can be standard (16-bit short form) or custom.

## V

**Vulnerability**
- Weakness in system that can be exploited. Examples: weak pairing, unencrypted communication, buffer overflow.

**Vulnerability Disclosure**
- Process of responsibly reporting security vulnerabilities to vendor.

## W

**Whitelist/Allowlist**
- Security control that explicitly permits specific devices, applications, or commands.

**Wireshark**
- Network protocol analyzer used for packet capture and analysis.

## X

**XSS (Cross-Site Scripting)**
- Web vulnerability where attacker injects malicious scripts into trusted websites.

## Z

**Zero-Day**
- Vulnerability unknown to vendor, with no available patch.

---

## Common Acronyms

| Acronym | Full Form |
|---------|-----------|
| AES | Advanced Encryption Standard |
| ATT | Attribute Protocol |
| BLE | Bluetooth Low Energy |
| C2 | Command and Control |
| CVE | Common Vulnerabilities and Exposures |
| DoS | Denial of Service |
| ECDH | Elliptic Curve Diffie-Hellman |
| EDR | Endpoint Detection and Response |
| GAP | Generic Access Profile |
| GATT | Generic Attribute Profile |
| HCI | Host Controller Interface |
| HID | Human Interface Device |
| IoT | Internet of Things |
| IRK | Identity Resolving Key |
| L2CAP | Logical Link Control and Adaptation Protocol |
| LE | Low Energy |
| MAC | Media Access Control |
| MFA | Multi-Factor Authentication |
| MITM | Man-in-the-Middle |
| NFC | Near Field Communication |
| OOB | Out of Band |
| PII | Personally Identifiable Information |
| PIN | Personal Identification Number |
| PoC | Proof of Concept |
| RFCOMM | Radio Frequency Communication |
| RPA | Resolvable Private Address |
| RSSI | Received Signal Strength Indicator |
| SMP | Security Manager Protocol |
| SPP | Serial Port Profile |
| TOTP | Time-based One-Time Password |
| UUID | Universally Unique Identifier |

---

[← Back to Wiki](../README.md)
