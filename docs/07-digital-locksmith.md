# Digital Locksmith & Physical Security

## Overview

Digital locksmithing combines traditional physical security knowledge with modern cybersecurity skills to understand, test, and secure electronic and BLE-enabled locks. This module bridges the gap between physical and digital security, essential for comprehensive security assessments.

## Table of Contents

1. [Introduction to Digital Locksmithing](#introduction-to-digital-locksmithing)
2. [Traditional vs. Digital Locks](#traditional-vs-digital-locks)
3. [BLE Lock Mechanisms](#ble-lock-mechanisms)
4. [Physical Security Fundamentals](#physical-security-fundamentals)
5. [Digital Lock Vulnerabilities](#digital-lock-vulnerabilities)
6. [Hybrid Attack Techniques](#hybrid-attack-techniques)
7. [Lock Picking vs. Hacking](#lock-picking-vs-hacking)
8. [Building Secure Digital Locks](#building-secure-digital-locks)

## Introduction to Digital Locksmithing

Digital locksmithing is the practice of understanding, analyzing, and manipulating electronic locking mechanisms. It requires knowledge from multiple domains:

- **Traditional Locksmithing**: Understanding mechanical principles
- **Electronics**: Circuit analysis, microcontrollers, sensors
- **Cybersecurity**: Encryption, authentication, protocol analysis
- **RF/Wireless**: BLE, NFC, RFID technologies
- **Physical Security**: Facility security, access control

### Why It Matters

1. **Comprehensive Security**: Many security assessments ignore physical access
2. **Real-World Impact**: Digital locks protect homes, offices, vehicles, data centers
3. **Attack Surface**: Combines physical and digital vulnerabilities
4. **Critical Infrastructure**: Increasingly used in essential facilities
5. **Legal Implications**: Physical access can lead to data breaches

### Career Paths

- Physical Penetration Tester (Red Team)
- Lock Security Consultant
- Access Control System Designer
- IoT Security Specialist
- Security Researcher
- Forensic Investigator

## Traditional vs. Digital Locks

### Traditional Mechanical Locks

**Components**:
- Cylinder (plug)
- Pins (driver and key pins)
- Springs
- Shear line
- Key

**Security Characteristics**:
- No power required
- Purely mechanical
- Vulnerable to picking, bumping, drilling
- Difficult to remotely compromise
- No audit trail

### Digital/Electronic Locks

**Components**:
- Microcontroller/processor
- Credential reader (keypad, card, BLE)
- Motor/solenoid (actuator)
- Power supply (battery/wired)
- Firmware
- Optional: Network connectivity

**Security Characteristics**:
- Requires power (battery/electric)
- Programmable logic
- Remote access possible
- Audit trail capability
- Vulnerable to both physical and digital attacks
- Can be updated (pro and con)

### Hybrid Smart Locks

**Components**:
- All traditional lock components
- Electronic controller overlay
- BLE/WiFi communication
- Mobile app interface
- Backup mechanical key

**Security Characteristics**:
- Multiple attack surfaces
- Failover to mechanical access
- Complex security model
- Convenience vs. security tradeoffs

## BLE Lock Mechanisms

### Common BLE Lock Types

#### 1. Deadbolt Overlay

```
┌─────────────────────────────────┐
│  Existing Deadbolt              │
│                                 │
│  ┌───────────────────────────┐  │
│  │  BLE Smart Lock Module    │  │
│  │  - Motor                  │  │
│  │  - BLE chip               │  │
│  │  - Batteries              │  │
│  │  - Turn mechanism         │  │
│  └───────────────────────────┘  │
│                                 │
└─────────────────────────────────┘
```

**Characteristics**:
- Retrofits existing deadbolt
- Inside mounting only
- Maintains mechanical key access
- Easy installation

**Vulnerabilities**:
- Original lock vulnerabilities remain
- Motor mechanism may be weak
- Battery dependency
- Physical access to electronics from inside

#### 2. Integrated Smart Lock

```
┌─────────────────────────────────┐
│  Complete Smart Lock Assembly   │
│                                 │
│  - Custom deadbolt              │
│  - Integrated BLE module        │
│  - Touchscreen (optional)       │
│  - Backup mechanical override   │
│  - Tamper sensors               │
│                                 │
└─────────────────────────────────┘
```

**Characteristics**:
- Purpose-built design
- Replaces entire lock
- Often includes additional features
- Professional installation recommended

**Vulnerabilities**:
- All-in-one failure point
- Expensive to replace if compromised
- Firmware vulnerabilities affect whole unit
- Backup key may be weak

#### 3. Smart Padlock

```
┌────────────┐
│  ┌──────┐  │
│  │ BLE  │  │ ← Shackle
│  └──────┘  │
│            │
│ ┌────────┐ │
│ │Battery │ │ ← Body (electronics inside)
│ └────────┘ │
│            │
└────────────┘
```

**Characteristics**:
- Portable
- No installation required
- App-based unlocking
- Often lacks mechanical backup

**Vulnerabilities**:
- Shackle attacks (cutting, shimming)
- Body attacks (drilling, crushing)
- Often limited security features
- Waterproofing challenges electronics access

#### 4. Smart Cylinder/Euro Profile

```
┌─────────────────────────────────┐
│  Euro Profile Cylinder          │
│  ┌───────────────────────────┐  │
│  │ Mechanical pins + BLE     │  │
│  │ reader integrated         │  │
│  └───────────────────────────┘  │
│                                 │
└─────────────────────────────────┘
```

**Characteristics**:
- Replaces cylinder only
- Common in Europe
- Dual credential (key + BLE)
- Compact electronics

**Vulnerabilities**:
- Constrained space limits security features
- Cylinder attacks still applicable
- Battery placement challenges

### How BLE Locks Physically Unlock

#### Motor-Based Systems

```
BLE Command → Controller → Motor Driver → DC Motor → Gear Train → Deadbolt
```

**Security Considerations**:
- Motor can be back-driven (forcing mechanism)
- Gear train may be plastic (weak)
- Current sensing can detect forcing
- Motor noise reveals operation

**Attack Vector**: Physical manipulation
```python
# Detect motor operation patterns
import time

def detect_motor_current():
    """Monitor current draw to reverse-engineer motor control"""
    # If you can access power lines
    current_samples = []
    
    while True:
        current = read_current_sensor()
        current_samples.append(current)
        
        if current > THRESHOLD:
            # Motor is operating
            duration = measure_motor_duration()
            print(f"Unlock motor ran for {duration}ms")
            
            # Replicate motor activation
            # activate_motor(duration)
```

#### Solenoid-Based Systems

```
BLE Command → Controller → Solenoid Driver → Solenoid → Latch Release
```

**Security Considerations**:
- Sudden electromagnetic pulse
- May be susceptible to magnetic attacks
- Faster operation than motor
- Distinct clicking sound

**Attack Vector**: Electromagnetic interference
```
External magnet → Solenoid activation → Unlatch
```

#### Servo-Based Systems

```
BLE Command → Controller → Servo Driver → Servo → Rotational Mechanism → Lock
```

**Security Considerations**:
- Precise control
- Feedback mechanism
- Can detect forced movement
- Limited torque

### Lock State Detection

Smart locks must know their current state:

**Methods**:
1. **Microswitch**: Physical switch indicates bolt position
2. **Hall Effect Sensor**: Magnetic sensor detects position
3. **Current Sensing**: Motor current indicates position
4. **Encoder**: Rotational encoder tracks movement
5. **Timeout**: Assume state based on motor run time

**Vulnerability**: State confusion attacks
```python
# If lock trusts internal state without verification
def state_confusion_attack():
    """
    Cause lock to believe it's in wrong state
    May trigger auto-unlock or expose unlock mechanism
    """
    # 1. Interrupt lock during operation
    send_command("UNLOCK")
    time.sleep(0.1)
    send_command("LOCK")  # Interrupt mid-operation
    
    # 2. Lock may be in confused state
    # 3. May respond incorrectly to next command
    send_command("STATUS")  # What does it think its state is?
```

## Physical Security Fundamentals

### Security Principles

#### Defense in Depth

Multiple layers of security:
1. **Perimeter**: Fence, walls
2. **Building Envelope**: Locks, reinforced doors
3. **Interior**: Room locks, safes
4. **Asset**: Encrypted data, secure containers

**Digital Lock Role**: One layer; should not be sole security

#### Fail-Safe vs. Fail-Secure

**Fail-Safe** (Unlocks on power loss):
- Fire exits
- Emergency egress
- Life safety priority

**Fail-Secure** (Remains locked on power loss):
- Data centers
- Secure rooms
- Asset protection priority

**BLE Lock Consideration**: Battery-powered locks must define behavior on battery death

#### Tamper Evidence vs. Tamper Resistance

**Tamper Evidence**: Shows that attack occurred
- Seals, indicators, audit logs
- Doesn't prevent attack
- Enables detection and response

**Tamper Resistance**: Prevents or delays attack
- Hardened enclosures
- Anti-drill plates
- Secure elements

**BLE Locks**: Should implement both

### Physical Attack Methods

#### 1. Shimming

**Traditional Locks**: Inserting thin metal to manipulate latch

**BLE Smart Locks**:
- Less effective on deadbolts
- May work on latch-based smart locks
- Some smart padlocks vulnerable

#### 2. Bumping

**Traditional Locks**: Using special key to "bump" pins

**BLE Smart Locks**:
- Electronic locks often maintain mechanical cylinder
- If backup key exists, may be vulnerable to bumping
- Some manufacturers use bump-resistant cylinders

#### 3. Drilling

**Target**: Destroy lock mechanism or access electronics

**BLE Smart Locks**:
- Can drill to access electronics
- May short circuit to cause malfunction
- Some have anti-drill plates

**Defense**:
- Hardened steel inserts
- Anti-drill plates
- Tamper detection
- Secure component placement

#### 4. Forcing/Prying

**Method**: Apply force to separate lock from door/frame

**BLE Smart Locks**:
- No stronger than mounting hardware
- Reinforced strikes help
- Door/frame strength matters more

**Defense**:
- Long throw deadbolts
- Reinforced strike plates
- Security hinges
- Quality door/frame

#### 5. Disassembly

**Method**: Remove screws, separate components

**BLE Smart Locks**:
- Interior electronics often exposed
- Tamper-resistant screws help
- Enclosure design critical

**Defense**:
- Security screws (Torx, one-way)
- Glued/sealed enclosures
- Tamper switches
- Internal battery vs. external access

## Digital Lock Vulnerabilities

### Hardware Vulnerabilities

#### 1. Debug Interfaces Left Enabled

**Issue**: JTAG, SWD, or UART accessible

**Exploitation**:
```bash
# Using OpenOCD with JTAG
openocd -f interface/jlink.cfg -f target/nrf52.cfg

# Connect with GDB
arm-none-eabi-gdb
(gdb) target remote localhost:3333
(gdb) monitor reset halt
(gdb) x/32x 0x00000000  # Dump firmware
```

**Impact**: Firmware extraction, reverse engineering, key retrieval

**Defense**:
- Disable debug interfaces in production
- Fuse blow/lock bits
- Debug authentication
- Secure debug

#### 2. Unencrypted External Flash

**Issue**: Firmware/keys stored in plaintext on external memory

**Exploitation**:
```python
# Read SPI flash with Bus Pirate or similar
import spidev

spi = spidev.SpiDev()
spi.open(0, 0)
spi.max_speed_hz = 1000000

# Read flash command (depends on flash chip)
READ_CMD = 0x03
address = 0x000000

# Send read command
data = spi.xfer2([READ_CMD, (address >> 16) & 0xFF, 
                   (address >> 8) & 0xFF, address & 0xFF, 
                   0x00] * 1024)  # Read 1KB

# Analyze dumped data for keys, credentials
```

**Impact**: Complete compromise

**Defense**:
- Encrypt external storage
- Use internal flash only
- Store keys in secure element
- Authenticated encryption

#### 3. Power Analysis Side-Channel

**Issue**: Cryptographic operations leak information via power consumption

**Exploitation**:
```python
# Simplified concept - real attacks use oscilloscopes and statistical analysis
import numpy as np

def power_analysis_attack():
    """
    Capture power traces during crypto operations
    Correlate power consumption with key bits
    """
    power_traces = []
    
    for plaintext in range(256):
        # Trigger encryption with known plaintext
        trigger_encryption(plaintext)
        
        # Capture power trace
        trace = capture_power_trace()
        power_traces.append(trace)
    
    # Statistical analysis to recover key
    key_candidates = correlate_power_to_key(power_traces)
    return most_likely_key(key_candidates)
```

**Impact**: Key extraction

**Defense**:
- Constant-time crypto implementations
- Power consumption randomization
- Filtering/decoupling
- Secure elements with DPA countermeasures

#### 4. Fault Injection

**Issue**: Inducing faults to bypass security checks

**Methods**:
- Voltage glitching
- Clock glitching
- Electromagnetic interference
- Laser fault injection

**Example Attack**:
```c
// Vulnerable authentication code
bool authenticate(uint8_t* credential) {
    bool authorized = false;
    
    // Check credential
    if (verify_credential(credential)) {
        authorized = true;  // ← Glitch here to skip
    }
    
    if (authorized) {
        unlock();
    }
}

// Glitch during the comparison can cause
// the condition to incorrectly evaluate to true
```

**Impact**: Authentication bypass, code execution

**Defense**:
- Voltage/clock monitors
- Redundant security checks
- Secure elements
- Shielding

### Firmware Vulnerabilities

#### 1. Buffer Overflows

**Issue**: Improper bounds checking

**Exploitation**:
```python
# Send oversized credential to overflow buffer
large_credential = b"A" * 1000

# If lock doesn't check length:
# - May crash (DoS)
# - May overwrite return address (RCE)
# - May overwrite authorization flag

write_characteristic(lock, "credential_char_uuid", large_credential)
```

**Impact**: Crash, code execution, privilege escalation

**Defense**:
- Bounds checking
- Safe string functions
- Stack canaries
- ASLR (if applicable)

#### 2. Integer Overflows

**Issue**: Arithmetic operations overflow

**Example**:
```c
// Vulnerable code
uint8_t pin_attempts = 0;
const uint8_t MAX_ATTEMPTS = 5;

void check_pin(uint16_t pin) {
    if (pin_attempts >= MAX_ATTEMPTS) {
        return;  // Locked out
    }
    
    pin_attempts++;  // ← Can overflow back to 0!
    
    if (verify_pin(pin)) {
        unlock();
    }
}
```

**Exploitation**:
```python
# Trigger overflow
for i in range(256):
    attempt_pin("0000")

# After 256 attempts, counter wrapped to 0
# Can attempt again
```

**Impact**: Bypass rate limiting, unexpected behavior

**Defense**:
- Check for overflow
- Use wider data types
- Saturating arithmetic
- Proper variable types

#### 3. Race Conditions

**Issue**: Time-of-check to time-of-use (TOCTOU) vulnerabilities

**Example**:
```c
// Vulnerable code
bool check_access() {
    // Check authorization
    if (is_authorized()) {
        // Time gap here!
        return true;
    }
    return false;
}

void unlock_sequence() {
    if (check_access()) {
        // Time gap here too!
        actuate_lock();
    }
}
```

**Exploitation**:
```python
import threading

def exploit_race_condition():
    def spam_requests():
        while True:
            request_unlock()
    
    # Start multiple threads
    for _ in range(10):
        t = threading.Thread(target=spam_requests)
        t.start()
    
    # May win race condition and unlock without proper auth
```

**Impact**: Authorization bypass

**Defense**:
- Atomic operations
- Proper locking/mutexes
- State machines
- Transaction-based logic

## Hybrid Attack Techniques

### Attack 1: Digital Unlock + Physical Manipulation

**Scenario**: Combine BLE attack with physical forcing

**Steps**:
1. Use BLE exploit to partially engage unlock mechanism
2. Apply physical force at critical moment
3. Lock mechanism fails to re-engage
4. Gain access

**Mitigation**:
- Robust mechanical design
- State verification
- Force detection sensors
- Fail-secure mechanisms

### Attack 2: Physical Access to Electronics

**Scenario**: Defeat physical security to access BLE internals

**Steps**:
1. Drill small hole to access PCB
2. Connect to debug interface or flash chip
3. Extract firmware and keys
4. Exploit via BLE with stolen keys

**Mitigation**:
- Hardened enclosures
- Tamper detection
- Secure elements (keys inaccessible even with physical access)
- Encapsulation/potting of electronics

### Attack 3: Power Manipulation

**Scenario**: Cause malfunction by manipulating power

**Steps**:
1. Identify power source (battery terminals)
2. Induce voltage glitch during critical operation
3. Cause unlock or authentication bypass

**Example**:
```python
def power_glitch_attack():
    """
    Pseudocode for power glitching attack
    Requires hardware access to power rails
    """
    # Wait for lock to enter authentication routine
    wait_for_authentication()
    
    # At critical moment, glitch power
    trigger_glitch()  # Brief voltage drop
    
    # May cause:
    # - Instruction skip
    # - Corrupt comparison
    # - Bypass security check
```

**Mitigation**:
- Power supply filtering
- Brown-out detection
- Voltage monitoring
- Redundant power sources

### Attack 4: Relay Attack

**Scenario**: Extend range of legitimate credential

**Architecture**:
```
Victim's Phone    Attacker Relay 1    Attacker Relay 2    Smart Lock
  (in house)      (near phone)        (at door)           (on door)
      │                 │                    │                 │
      │                 │                    │                 │
      │←─── BLE ────────│                    │                 │
      │                 │                    │                 │
      │                 │←──── Internet ────→│                 │
      │                 │                    │                 │
      │                 │                    │───── BLE ──────→│
      │                 │                    │                 │
      │                 │                    │←─── Unlock ─────│
```

**Implementation**:
```python
# Relay 1 (near victim's phone)
def relay_one():
    # Scan for victim's phone
    phone = find_target_phone()
    
    # Connect via BLE
    connection = connect_ble(phone)
    
    # Forward all traffic to Relay 2 via internet
    while True:
        data = connection.read()
        send_to_relay2(data)
        
        response = receive_from_relay2()
        connection.write(response)

# Relay 2 (at lock)
def relay_two():
    # Connect to lock
    lock = connect_ble(LOCK_MAC)
    
    # Forward all traffic to Relay 1
    while True:
        data = lock.read()
        send_to_relay1(data)
        
        response = receive_from_relay1()
        lock.write(response)

# Result: Lock thinks it's talking to nearby phone
# Phone thinks it's talking to nearby lock
# Attacker transparently relays between them
```

**Mitigation**:
- Distance bounding protocols
- Latency measurement
- User confirmation required
- Proximity verification (RSSI, RTT)

## Lock Picking vs. Hacking

### Traditional Lock Picking

**Skills Required**:
- Tactile feedback
- Understanding of pin mechanics
- Practice and patience
- Tool manipulation

**Tools**:
- Picks (hook, rake, etc.)
- Tension wrench
- Plug spinner
- Bump keys

**Time**: Seconds to minutes (skill-dependent)

### Digital Lock "Picking" (Hacking)

**Skills Required**:
- Protocol analysis
- Programming
- Cryptography knowledge
- Reverse engineering
- Electronics

**Tools**:
- BLE adapters/sniffers
- Software (Python, custom scripts)
- Logic analyzers
- Debuggers (JTAG, SWD)

**Time**: Minutes to months (vulnerability-dependent)

### Hybrid Approach

**Modern Security Professional**: Should understand both

**Reasoning**:
1. Many smart locks retain mechanical backup
2. Physical security can defeat digital security
3. Digital security can defeat physical security
4. Comprehensive assessment requires both
5. Defense design requires understanding both attack vectors

### Educational Value

**Traditional Lockpicking**:
- Teaches security through obscurity fails
- Demonstrates physical security limitations
- Develops understanding of mechanical design
- Legal considerations (varies by jurisdiction)

**Note**: Practice only on locks you own or have explicit permission to pick

## Building Secure Digital Locks

### Security Requirements

#### Functional Security

1. **Strong Authentication**
   - Multi-factor where possible
   - Cryptographically secure
   - Resistant to replay, brute force, MitM

2. **Encrypted Communication**
   - All sensitive data encrypted
   - Use BLE Secure Connections
   - Implement application-layer encryption

3. **Access Control**
   - Granular permissions
   - Role-based access
   - Time-based restrictions

4. **Audit Logging**
   - Log all access attempts
   - Tamper-evident logs
   - Secure log storage

#### Physical Security

1. **Tamper Detection**
   - Switches for cover removal
   - Accelerometers for physical attack detection
   - Alert on tampering

2. **Secure Hardware**
   - Secure elements for key storage
   - Anti-tamper enclosure
   - Debug interface protection

3. **Robust Mechanism**
   - Strong actuator
   - Force detection
   - Fail-secure design

4. **Power Management**
   - Low battery warnings
   - Graceful degradation
   - Define fail-secure/fail-safe clearly

### Reference Design: Secure BLE Lock

**Architecture**:
```
┌─────────────────────────────────────────────┐
│  Secure BLE Lock                            │
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │  Microcontroller (ARM Cortex-M)     │   │
│  │  - BLE Stack                        │   │
│  │  - Application Logic                │   │
│  │  - Cryptography                     │   │
│  └─────────┬───────────────────────────┘   │
│            │                                │
│  ┌─────────▼──────────┐  ┌──────────────┐  │
│  │  Secure Element    │  │  Motor       │  │
│  │  (ATECC608/similar)│  │  Driver      │  │
│  │  - Key Storage     │  └──────┬───────┘  │
│  │  - Crypto Accel    │         │          │
│  └────────────────────┘  ┌──────▼───────┐  │
│                          │  Deadbolt    │  │
│  ┌────────────────────┐  │  Mechanism   │  │
│  │ Tamper Detection   │  └──────────────┘  │
│  │ - Cover switch     │                     │
│  │ - Accelerometer    │  ┌──────────────┐  │
│  └────────────────────┘  │  Battery     │  │
│                          │  Monitor     │  │
│                          └──────────────┘  │
└─────────────────────────────────────────────┘
```

**Security Features**:
1. Secure element stores keys (hardware-protected)
2. Challenge-response with nonce
3. BLE Secure Connections with Numeric Comparison
4. Encrypted firmware
5. Signed firmware updates
6. Tamper detection with alert
7. Audit logging
8. Rate limiting on auth attempts
9. Time-based access restrictions
10. Battery tamper detection

**Sample Authentication Flow**:
```python
# Simplified secure authentication

class SecureBLELock:
    def __init__(self, secure_element):
        self.se = secure_element
        self.failed_attempts = 0
        self.lockout_time = 0
    
    def authenticate_device(self, device_id, signature):
        # Check rate limiting
        if self.is_locked_out():
            return {"status": "locked_out", "retry_after": self.lockout_time}
        
        # Generate random challenge
        challenge = self.se.generate_random(32)
        
        # Send challenge to device
        response = self.send_challenge(challenge)
        
        # Verify response using secure element
        if self.se.verify_signature(device_id, challenge, response):
            self.failed_attempts = 0
            self.log_access(device_id, "success")
            return {"status": "authenticated", "session_key": self.establish_session()}
        else:
            self.failed_attempts += 1
            self.log_access(device_id, "failed")
            
            if self.failed_attempts >= 5:
                self.lockout_time = time.time() + 300  # 5 min lockout
                self.alert_admin("Multiple failed auth attempts")
            
            return {"status": "denied"}
    
    def is_locked_out(self):
        return time.time() < self.lockout_time
    
    def establish_session(self):
        # ECDH key exchange for session key
        return self.se.ecdh_key_exchange()
    
    def unlock(self, session_key, encrypted_command):
        # Decrypt command using session key
        command = self.se.decrypt(session_key, encrypted_command)
        
        # Verify command is "UNLOCK"
        if command == b"UNLOCK" and self.verify_timestamp(command):
            # Check authorization
            if self.is_authorized_for_unlock():
                self.actuate_motor()
                self.log_access(session_key, "unlocked")
                return {"status": "unlocked"}
        
        return {"status": "unauthorized"}
```

## Practical Lab: Build a Secure BLE Lock

### Objective
Design and implement a secure BLE lock system

### Hardware Needed
- ESP32 or nRF52 development board
- Servo motor or solenoid
- Power supply
- (Optional) Secure element chip
- (Optional) Tamper switch

### Software Components
1. BLE stack configuration
2. Authentication implementation
3. Command processing
4. Actuator control
5. Logging system

### Implementation Steps

**Phase 1**: Basic functionality
- BLE advertising and connection
- Simple command processing
- Motor control

**Phase 2**: Security hardening
- Implement challenge-response
- Add encryption
- Rate limiting

**Phase 3**: Advanced features
- Audit logging
- Tamper detection
- Firmware update mechanism

**Phase 4**: Security testing
- Self-pentest
- Document vulnerabilities
- Implement fixes

### Assessment
- Does it meet security requirements?
- Can you break your own implementation?
- How does it compare to commercial products?

## Quiz

1. What are the main differences between mechanical and digital locks in terms of attack surface?
2. Why is fail-secure behavior important for data center locks?
3. Describe a hybrid attack combining physical and digital techniques.
4. What is a relay attack and how can it be mitigated?
5. Why should debug interfaces be disabled in production?
6. What role does a secure element play in digital lock security?
7. How can power analysis attacks compromise lock security?
8. Describe the security advantages and disadvantages of backup mechanical keys.

## Next Steps

Continue to [Secure SMS Proxy Integration →](08-secure-sms-proxy-integration.md)

## Additional Resources

- [Lockpicking Detail Overkill](http://www.lockpickingforensics.com/)
- [Hardware Security Training (Riscure)](https://www.riscure.com/security-training/)
- [IoT Security Foundation Best Practices](https://www.iotsecurityfoundation.org/)
- Books:
  - "Practical Lock Picking" by Deviant Ollam
  - "The Hardware Hacker" by Andrew "bunnie" Huang

---

[← Back: BLE Access Control](06-ble-access-control.md) | [Next: Secure SMS Proxy Integration →](08-secure-sms-proxy-integration.md)
