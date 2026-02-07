# BLE Cybersecurity & Pentesting Learning Wiki

Welcome to the comprehensive educational resource for Bluetooth Low Energy (BLE) cybersecurity, pentesting, and digital locksmith training using the HID Barcode Scanner app.

## 📚 Table of Contents

### Core Concepts
1. [Introduction to BLE Security](01-ble-security-introduction.md)
2. [Bluetooth HID Protocol](02-bluetooth-hid-protocol.md)
3. [BLE Attack Vectors](03-ble-attack-vectors.md)

### Practical Applications
4. [Using HID Barcode Scanner for Security Testing](04-hid-scanner-pentesting.md)
5. [RFCOMM Mode for Advanced Testing](05-rfcomm-advanced-testing.md)
6. [BLE Access Control Systems](06-ble-access-control.md)
7. [Digital Locksmith & Physical Security](07-digital-locksmith.md)

### Integration & Advanced Topics
8. [Secure SMS Proxy Integration](08-secure-sms-proxy-integration.md)
9. [Building Security Test Labs](09-security-test-labs.md)
10. [Ethical Hacking Guidelines](10-ethical-guidelines.md)

### Hands-On Exercises
11. [Exercise 1: HID Injection Basics](exercises/ex01-hid-injection-basics.md)
12. [Exercise 2: BLE Reconnaissance](exercises/ex02-ble-reconnaissance.md)
13. [Exercise 3: Access Control Testing](exercises/ex03-access-control-testing.md)
14. [Exercise 4: SMS-Based Authentication](exercises/ex04-sms-authentication.md)
15. [Exercise 5: Digital Lock Analysis](exercises/ex05-digital-lock-analysis.md)
16. [Exercise 6: Credential Capture Detection](exercises/ex06-credential-capture.md)
17. [Exercise 7: Building Defense Systems](exercises/ex07-building-defense.md)

### Reference Materials
- [Tools & Software](reference/tools.md)
- [BLE Security Standards](reference/standards.md)
- [Glossary](reference/glossary.md)
- [Additional Resources](reference/resources.md)

## 🎯 Learning Objectives

By completing this curriculum, students will:

1. **Understand BLE Security Fundamentals**
   - BLE protocol stack and architecture
   - Security mechanisms (pairing, bonding, encryption)
   - Common vulnerabilities and attack surfaces

2. **Master Pentesting Techniques**
   - HID injection attacks
   - BLE device reconnaissance and enumeration
   - Man-in-the-middle (MitM) attacks
   - Replay attacks
   - Denial of service (DoS) attacks

3. **Analyze Access Control Systems**
   - Digital lock mechanisms
   - BLE-based authentication systems
   - Physical security bypass techniques
   - Vulnerability assessment

4. **Implement Security Solutions**
   - Defense strategies
   - Secure communication protocols
   - Multi-factor authentication (MFA)
   - Intrusion detection systems

5. **Apply Ethical Hacking Principles**
   - Legal and ethical considerations
   - Responsible disclosure
   - Professional conduct
   - Documentation and reporting

## 🔧 Prerequisites

### Hardware Requirements
- Android device (Android 9 or higher) with Bluetooth HID support
- Computer with Bluetooth 4.0+ support
- (Optional) BLE development boards (ESP32, nRF52, etc.)
- (Optional) BLE-enabled smart locks for testing
- (Optional) USB Bluetooth adapter for enhanced testing

### Software Requirements
- HID Barcode Scanner app (this repository)
- Android Studio (for app modifications)
- Python 3.8+ with relevant libraries
- BLE scanning tools (nRF Connect, Wireshark, etc.)
- Secure SMS Proxy (for SMS integration exercises)

### Knowledge Prerequisites
- Basic understanding of networking concepts
- Familiarity with Android/mobile platforms
- Basic programming skills (Python, JavaScript)
- Understanding of cryptography fundamentals
- Linux command line basics

## 🚀 Getting Started

1. **Installation**
   ```bash
   # Clone the repository
   git clone https://github.com/Fabi019/hid-barcode-scanner.git
   cd hid-barcode-scanner
   
   # Build the app using Android Studio or
   ./gradlew assembleDebug
   ```

2. **Initial Setup**
   - Install the app on your Android device
   - Pair with a test computer
   - Verify HID functionality works
   - Test RFCOMM mode connectivity

3. **Lab Environment**
   - Set up isolated testing environment
   - Install required pentesting tools
   - Configure network monitoring
   - Prepare documentation templates

4. **Begin Learning**
   - Start with [Introduction to BLE Security](01-ble-security-introduction.md)
   - Follow the curriculum in order
   - Complete exercises at your own pace
   - Join community discussions

## ⚖️ Legal & Ethical Notice

**IMPORTANT:** This educational material is intended for:
- Authorized security testing
- Academic research
- Professional penetration testing with written permission
- Personal devices and systems you own

**NEVER:**
- Test on systems without explicit written authorization
- Use these techniques for unauthorized access
- Attempt to bypass security measures on production systems
- Violate local, state, or federal laws

Unauthorized access to computer systems is illegal in most jurisdictions. Always obtain proper authorization and follow responsible disclosure guidelines.

## 🤝 Contributing

We welcome contributions to improve this educational resource:
- Submit corrections and improvements
- Share additional exercises
- Provide real-world case studies
- Translate materials to other languages

See [CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines.

## 📖 Citation

If you use this curriculum in academic work, please cite:

```
HID Barcode Scanner BLE Security Curriculum
https://github.com/Fabi019/hid-barcode-scanner
Educational materials for Bluetooth Low Energy cybersecurity and pentesting
```

## 📞 Support & Community

- GitHub Issues: Report bugs or request features
- Discussions: Ask questions and share knowledge
- Security Researchers: Responsible disclosure via email

## 📄 License

Educational materials: CC BY-SA 4.0
HID Barcode Scanner app: GNU General Public License v3.0

---

**Ready to begin?** Start with [Introduction to BLE Security →](01-ble-security-introduction.md)
