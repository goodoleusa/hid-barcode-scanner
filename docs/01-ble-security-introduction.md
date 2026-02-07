# Introduction to BLE Security

## Overview

Bluetooth Low Energy (BLE), also known as Bluetooth Smart, is a wireless personal area network technology designed for short-range communication with low power consumption. Understanding BLE security is crucial for modern cybersecurity professionals, as BLE is ubiquitous in IoT devices, smartphones, wearables, medical devices, and access control systems.

## Table of Contents

1. [What is BLE?](#what-is-ble)
2. [BLE vs Classic Bluetooth](#ble-vs-classic-bluetooth)
3. [BLE Protocol Stack](#ble-protocol-stack)
4. [Security Mechanisms](#security-mechanisms)
5. [Common Vulnerabilities](#common-vulnerabilities)
6. [Why BLE Security Matters](#why-ble-security-matters)

## What is BLE?

Bluetooth Low Energy (BLE) was introduced in Bluetooth 4.0 specification in 2010. It's designed for applications requiring:
- Low power consumption (battery life of months to years)
- Short burst communication
- Low data rate (typically under 1 Mbps)
- Small data packets
- Quick connection establishment

### Key Characteristics

| Feature | Specification |
|---------|---------------|
| Frequency | 2.4 GHz ISM band |
| Channels | 40 channels (2 MHz spacing) |
| Range | Up to 100m (depending on power class) |
| Data Rate | 125 Kbps to 2 Mbps |
| Latency | As low as 3ms |
| Power | Microamps in standby, milliwatts active |

## BLE vs Classic Bluetooth

Understanding the differences helps identify distinct security considerations:

| Aspect | Classic Bluetooth | BLE |
|--------|------------------|-----|
| **Power Consumption** | Higher | Ultra-low |
| **Data Throughput** | Up to 3 Mbps | Up to 2 Mbps |
| **Range** | 10-100m | 10-100m |
| **Application** | Audio streaming, large data | Sensors, beacons, IoT |
| **Connection Time** | ~6 seconds | <6 milliseconds |
| **Pairing** | Complex | Simplified |
| **Security** | Legacy issues | Improved but new attack surfaces |

**Critical Difference for Pentesting:** BLE devices often have weaker authentication due to simplified pairing and limited user interfaces.

## BLE Protocol Stack

Understanding the protocol stack is essential for identifying attack surfaces:

```
┌─────────────────────────────────────┐
│        Application Layer            │  ← Your app code
├─────────────────────────────────────┤
│   Generic Attribute Profile (GATT)  │  ← Data structure
├─────────────────────────────────────┤
│   Attribute Protocol (ATT)          │  ← Data exchange
├─────────────────────────────────────┤
│   Security Manager Protocol (SMP)   │  ← Pairing & encryption
├─────────────────────────────────────┤
│   Logical Link Control (L2CAP)      │  ← Packet segmentation
├─────────────────────────────────────┤
│   Host Controller Interface (HCI)   │  ← Software/hardware boundary
├─────────────────────────────────────┤
│          Link Layer                 │  ← Advertising, connections
├─────────────────────────────────────┤
│        Physical Layer               │  ← Radio (2.4 GHz)
└─────────────────────────────────────┘
```

### Attack Surfaces by Layer

1. **Physical Layer**: Jamming, signal interference
2. **Link Layer**: Connection hijacking, tracking via MAC addresses
3. **L2CAP**: DoS via packet flooding
4. **SMP**: Pairing attacks, key extraction
5. **ATT/GATT**: Unauthorized attribute access, data leakage
6. **Application**: Logic flaws, insecure data handling

## Security Mechanisms

### 1. Pairing & Bonding

**Pairing** is the process of establishing shared secret keys.
**Bonding** is storing these keys for future connections.

#### Pairing Methods

| Method | Description | Security Level | Common Use |
|--------|-------------|----------------|------------|
| **Just Works** | No user interaction | Low | Devices without I/O |
| **Numeric Comparison** | User compares 6-digit codes | Medium | Smartphones, tablets |
| **Passkey Entry** | User enters PIN | Medium-High | Devices with keypads |
| **Out of Band (OOB)** | Key exchange via NFC/QR | High | Advanced devices |

**Security Note:** "Just Works" provides no man-in-the-middle (MitM) protection!

### 2. Encryption

BLE uses AES-128 CCM (Counter with CBC-MAC) for encryption.

- **Key Size**: 128 bits
- **Authentication**: CMAC
- **Encryption**: CTR mode
- **Applied At**: Link layer (after pairing)

### 3. Authentication

BLE 4.2+ includes Secure Connections with:
- Elliptic Curve Diffie-Hellman (ECDH) for key generation
- Numeric comparison for MitM protection
- Public key cryptography

### 4. Privacy Features

**Address Resolution**: Devices can use resolvable private addresses that periodically change to prevent tracking.

- **Static Address**: Fixed, easily trackable
- **Private Resolvable**: Changes periodically, requires IRK to resolve
- **Private Non-Resolvable**: Changes periodically, cannot be resolved

## Common Vulnerabilities

### 1. Weak Pairing Mechanisms

**Issue**: Many devices use "Just Works" pairing with no authentication.

**Impact**: Attackers can pair without user knowledge.

**Real-World Example**: Smart locks using Just Works can be paired by anyone in range.

### 2. Insecure Storage of Keys

**Issue**: Long-term keys stored in plaintext or with weak protection.

**Impact**: Key extraction enables device impersonation.

**Attack Vector**: Rooted devices, malware, physical access.

### 3. Insufficient Authorization

**Issue**: Connected devices can access sensitive GATT characteristics without proper authorization.

**Impact**: Unauthorized data access, command injection.

**Example**: Medical devices exposing patient data.

### 4. Lack of Encryption

**Issue**: Some implementations don't enforce encryption for sensitive data.

**Impact**: Eavesdropping on communication.

**Attack**: Passive sniffing with nRF Sniffer or Ubertooth.

### 5. MAC Address Privacy Issues

**Issue**: Static MAC addresses enable device tracking.

**Impact**: Privacy violation, location tracking.

**Mitigation**: Use resolvable private addresses (RPA).

### 6. Downgrade Attacks

**Issue**: Attackers force use of weaker security mechanisms.

**Impact**: Bypassing newer security features.

**Example**: Force use of BLE 4.0 instead of 4.2+ Secure Connections.

### 7. Replay Attacks

**Issue**: Lack of replay protection in application layer.

**Impact**: Replaying commands (e.g., "unlock door").

**Mitigation**: Implement nonces/timestamps at app level.

### 8. Denial of Service (DoS)

**Issue**: BLE devices are vulnerable to various DoS attacks.

**Types**:
- Connection flooding
- Packet injection
- Jamming
- Battery drain attacks

## Why BLE Security Matters

### Critical Applications Using BLE

1. **Healthcare**: Insulin pumps, pacemakers, glucose monitors
   - **Risk**: Life-threatening if compromised

2. **Access Control**: Smart locks, building access, vehicles
   - **Risk**: Unauthorized physical access

3. **Financial**: Mobile payments, POS systems
   - **Risk**: Financial fraud

4. **Personal Data**: Fitness trackers, smartphones
   - **Risk**: Privacy violations, data theft

5. **Industrial IoT**: Sensors, controllers, monitoring systems
   - **Risk**: Process disruption, sabotage

### Real-World Attacks

#### Case Study 1: Tesla Model S Key Fob (2018)
Researchers demonstrated relay attacks on BLE key fobs, allowing car theft by extending the range of the key fob signal.

#### Case Study 2: August Smart Lock (2016)
Vulnerability in BLE implementation allowed unauthorized access to smart locks.

#### Case Study 3: Fitness Tracker Data Leakage (2017)
Multiple fitness trackers exposed sensitive health and location data due to unencrypted BLE communication.

#### Case Study 4: Medical Device Vulnerabilities (Ongoing)
FDA has issued multiple warnings about BLE-enabled medical devices with security flaws.

## Security Best Practices

### For Developers

1. **Always use the strongest pairing method** available for your device capabilities
2. **Implement Secure Connections** (BLE 4.2+) with LE Secure Connections
3. **Enforce encryption** for all sensitive data
4. **Implement application-level security**: Don't rely solely on BLE security
5. **Use secure key storage**: Hardware-backed keystores, secure elements
6. **Validate all inputs**: Prevent injection attacks
7. **Implement replay protection**: Use nonces, timestamps, counters
8. **Enable privacy features**: Use resolvable private addresses
9. **Regular security audits**: Test for known vulnerabilities
10. **Timely updates**: Patch security issues quickly

### For Pentesters

1. **Understand the protocol**: Know the stack inside-out
2. **Use proper tools**: nRF Connect, Wireshark, Ubertooth, etc.
3. **Test all pairing methods**: Attempt MitM, eavesdropping
4. **Analyze GATT structure**: Map all services and characteristics
5. **Test authorization**: Try accessing characteristics without proper auth
6. **Sniff traffic**: Capture and analyze encrypted/unencrypted data
7. **Attempt DoS**: Test resilience to connection flooding
8. **Fuzz inputs**: Send malformed packets to find bugs
9. **Document findings**: Maintain detailed reports
10. **Follow ethical guidelines**: Only test authorized systems

## Hands-On: Identifying BLE Devices

### Exercise: Scan for BLE Devices

**Tools Needed**: nRF Connect for Mobile (Android/iOS)

**Steps**:
1. Install nRF Connect from Play Store
2. Open app and tap "SCAN"
3. Observe devices in range
4. Note:
   - Device names
   - MAC addresses
   - Signal strength (RSSI)
   - Advertised services

**Questions to Consider**:
- Can you identify device types by their advertised services?
- Are MAC addresses static or rotating?
- What information is exposed without pairing?

### Exercise: Analyze BLE Security Settings

**Using HID Barcode Scanner App**:
1. Install and open the app
2. Navigate to Settings
3. Examine security-related options
4. Note the pairing process when connecting to a PC

**Analysis**:
- What pairing method is used?
- Is encryption enforced?
- Can you identify security improvements?

## Quiz

Test your understanding:

1. What is the main difference between BLE and Classic Bluetooth in terms of power consumption?
2. Name three BLE pairing methods and their security levels.
3. What layer of the BLE stack handles encryption?
4. Why is "Just Works" pairing considered insecure?
5. What is a resolvable private address and why is it important?
6. Name three real-world applications where BLE security is critical.
7. What is the difference between pairing and bonding?
8. How can an attacker perform a MitM attack on BLE "Just Works" pairing?

## Next Steps

Continue to [Bluetooth HID Protocol →](02-bluetooth-hid-protocol.md) to understand how the HID Barcode Scanner app works and its security implications.

## Additional Resources

- [Bluetooth SIG Security Documentation](https://www.bluetooth.com/learn-about-bluetooth/bluetooth-technology/security/)
- [NIST Cybersecurity Guide for BLE](https://csrc.nist.gov/)
- [OWASP Mobile Security Testing Guide - BLE](https://owasp.org/www-project-mobile-security-testing-guide/)
- Research Paper: "Comprehensive Study of BLE Security" (IEEE)

---

[← Back to Wiki Home](README.md) | [Next: Bluetooth HID Protocol →](02-bluetooth-hid-protocol.md)
