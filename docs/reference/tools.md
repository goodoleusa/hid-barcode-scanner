# Tools & Software

## Essential Tools for BLE Security Testing

### Hardware Tools

#### Ubertooth One
- **Purpose**: Bluetooth packet sniffing and analysis
- **Cost**: ~$100-$150
- **Capabilities**: 
  - Capture BLE packets
  - Follow connections
  - Jam BLE signals
  - Sniff LE advertisements
- **Where to Buy**: [Great Scott Gadgets](https://greatscottgadgets.com/ubertooth/)
- **Documentation**: [Ubertooth Wiki](https://github.com/greatscottgadgets/ubertooth/wiki)

**Basic Usage**:
```bash
# Scan for BLE devices
ubertooth-btle -f

# Follow specific device
ubertooth-btle -f -t <MAC>

# Save to pcap
ubertooth-btle -f -c output.pcap
```

#### nRF52840 Dongle
- **Purpose**: BLE development and testing
- **Cost**: ~$10
- **Capabilities**:
  - Bluetooth 5.0 support
  - Can be programmed with custom firmware
  - Sniffer mode with Wireshark
- **Where to Buy**: [Nordic Semiconductor](https://www.nordicsemi.com/Products/Development-hardware/nRF52840-Dongle)

#### ESP32 Development Boards
- **Purpose**: BLE/WiFi testing and development
- **Cost**: ~$5-$20
- **Capabilities**:
  - BLE and WiFi
  - Arduino/ESP-IDF programmable
  - Can simulate BLE devices
- **Where to Buy**: Amazon, AliExpress, Adafruit

#### Android Devices with BLE
- **Purpose**: Mobile BLE testing (including HID Barcode Scanner)
- **Requirements**: Android 9+ for HID support
- **Recommended**: Pixel phones, Samsung Galaxy series
- **Alternative**: Any Android device with good Bluetooth support

### Software Tools

#### Mobile Applications

**nRF Connect for Mobile**
- **Platform**: Android, iOS
- **Purpose**: BLE scanning and GATT exploration
- **Cost**: Free
- **Features**:
  - Device scanning
  - GATT service/characteristic enumeration
  - Read/write characteristics
  - Advertising packet analysis
- **Download**: [Google Play](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp) | [App Store](https://apps.apple.com/us/app/nrf-connect/id1054362403)

**HID Barcode Scanner**
- **Platform**: Android 9+
- **Purpose**: BLE HID keyboard emulation, barcode scanning
- **Cost**: Free (open source)
- **Features**:
  - HID keyboard mode
  - RFCOMM mode
  - Template system for payloads
  - JavaScript engine
- **Download**: [GitHub Releases](https://github.com/Fabi019/hid-barcode-scanner/releases)

**BLE Scanner**
- **Platform**: Android, iOS
- **Purpose**: Basic BLE device scanning
- **Cost**: Free
- **Features**:
  - Simple interface
  - Device discovery
  - Basic service listing

#### Desktop Applications

**Wireshark**
- **Platform**: Windows, macOS, Linux
- **Purpose**: Packet analysis
- **Cost**: Free
- **Features**:
  - BLE packet dissection
  - Protocol analysis
  - Filter capabilities
  - Export options
- **Download**: [wireshark.org](https://www.wireshark.org/download.html)

**Installation**:
```bash
# Ubuntu/Debian
sudo apt install wireshark

# macOS
brew install wireshark

# Windows: Download installer from website
```

**Bluetooth Setup for Wireshark**:
```bash
# Linux: Enable Bluetooth HCI logging
sudo btmon

# In Wireshark, capture from "bluetooth-monitor" interface
```

**btlejack**
- **Platform**: Linux
- **Purpose**: BLE MITM and sniffing
- **Cost**: Free
- **Requirements**: Micro:bit or specific nRF51 boards
- **Features**:
  - Connection sniffing
  - MITM attacks
  - Key cracking
- **Installation**:
```bash
pip3 install btlejack
```

**Download**: [GitHub](https://github.com/virtualabs/btlejack)

#### Command-Line Tools

**bluetoothctl**
- **Platform**: Linux
- **Purpose**: Bluetooth management
- **Cost**: Free (built-in)

**Usage**:
```bash
# Launch bluetoothctl
bluetoothctl

# Scan for devices
scan on

# Pair with device
pair <MAC>

# Connect
connect <MAC>
```

**hcitool / hciconfig**
- **Platform**: Linux
- **Purpose**: Low-level Bluetooth control
- **Cost**: Free (built-in)

**Usage**:
```bash
# Scan for LE devices
sudo hcitool lescan

# Get device info
sudo hcitool leinfo <MAC>

# Configure HCI
sudo hciconfig hci0 up
```

**gatttool**
- **Platform**: Linux
- **Purpose**: GATT client tool
- **Cost**: Free

**Usage**:
```bash
# Connect to device
gatttool -b <MAC> -I

# List services and characteristics
[MAC][LE]> primary
[MAC][LE]> characteristics

# Read characteristic
[MAC][LE]> char-read-hnd 0x0003
```

### Python Libraries

**bluepy**
- **Purpose**: Python BLE library
- **Platform**: Linux
- **Installation**:
```bash
pip install bluepy
```

**Example**:
```python
from bluepy.btle import Scanner

scanner = Scanner()
devices = scanner.scan(10.0)

for dev in devices:
    print(f"Device {dev.addr} ({dev.addrType}), RSSI={dev.rssi} dB")
```

**pybluez**
- **Purpose**: Bluetooth library (Classic and LE)
- **Platform**: Cross-platform
- **Installation**:
```bash
pip install pybluez
```

**bleak**
- **Purpose**: Modern cross-platform BLE library
- **Platform**: Windows, macOS, Linux
- **Installation**:
```bash
pip install bleak
```

**Example**:
```python
import asyncio
from bleak import BleakScanner

async def scan():
    devices = await BleakScanner.discover()
    for d in devices:
        print(d)

asyncio.run(scan())
```

### Exploitation Frameworks

**Metasploit Framework**
- **Purpose**: Penetration testing framework
- **Platform**: Linux, macOS, Windows
- **Cost**: Free (Community Edition)
- **Features**:
  - Exploitation modules
  - Payload generation
  - Post-exploitation
- **Installation**:
```bash
# Kali Linux (pre-installed)
msfconsole

# Ubuntu/Debian
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall
chmod 755 msfinstall
./msfinstall
```

**Empire / Starkiller**
- **Purpose**: Post-exploitation framework
- **Platform**: Cross-platform
- **Features**:
  - PowerShell agents
  - C2 infrastructure
  - Privilege escalation
- **Download**: [GitHub](https://github.com/BC-SECURITY/Empire)

### Security Testing Distributions

**Kali Linux**
- **Purpose**: Penetration testing distribution
- **Cost**: Free
- **Pre-installed Tools**:
  - Wireshark, hcitool, gatttool
  - Metasploit
  - Many other security tools
- **Download**: [kali.org](https://www.kali.org/get-kali/)

**Parrot Security OS**
- **Purpose**: Security and forensics distribution
- **Cost**: Free
- **Similar to**: Kali Linux
- **Download**: [parrotsec.org](https://www.parrotsec.org/)

### Monitoring and Detection Tools

**Sysmon (Windows)**
- **Purpose**: System activity monitoring
- **Cost**: Free
- **Features**:
  - Process creation logging
  - Network connections
  - File creation
- **Download**: [Microsoft Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)

**Installation**:
```powershell
# Download Sysmon
# Install with configuration
Sysmon64.exe -accepteula -i sysmonconfig.xml
```

**auditd (Linux)**
- **Purpose**: Linux audit framework
- **Cost**: Free (built-in)
- **Installation**:
```bash
sudo apt install auditd audispd-plugins
sudo systemctl enable auditd
sudo systemctl start auditd
```

### Development Tools

**Android Studio**
- **Purpose**: Android app development (for modifying HID Scanner)
- **Platform**: Windows, macOS, Linux
- **Cost**: Free
- **Download**: [developer.android.com](https://developer.android.com/studio)

**Visual Studio Code**
- **Purpose**: Code editor for scripts
- **Platform**: Cross-platform
- **Cost**: Free
- **Recommended Extensions**:
  - Python
  - PowerShell
  - Markdown
- **Download**: [code.visualstudio.com](https://code.visualstudio.com/)

**PlatformIO**
- **Purpose**: Embedded development (ESP32, nRF52)
- **Platform**: Cross-platform
- **Cost**: Free
- **Installation**:
```bash
# As VS Code extension or standalone
pip install platformio
```

### Online Tools

**CyberChef**
- **Purpose**: Data encoding/decoding, cryptography
- **URL**: [gchq.github.io/CyberChef](https://gchq.github.io/CyberChef/)
- **Features**:
  - Base64, hex encoding
  - Hashing
  - Encryption/decryption

**Bluetooth UUID Database**
- **Purpose**: Look up standard Bluetooth UUIDs
- **URL**: [bluetooth.com/specifications/assigned-numbers](https://www.bluetooth.com/specifications/assigned-numbers/)

**Request Bin / Webhook.site**
- **Purpose**: Testing data exfiltration
- **URL**: [webhook.site](https://webhook.site/)
- **Use**: Receive HTTP requests for testing

## Tool Comparison Matrix

| Tool | Purpose | Platform | Cost | Difficulty |
|------|---------|----------|------|------------|
| Ubertooth One | Packet sniffing | Linux | $100-150 | Advanced |
| nRF Connect | GATT exploration | Mobile | Free | Beginner |
| HID Scanner | HID injection | Android | Free | Intermediate |
| Wireshark | Packet analysis | Multi | Free | Intermediate |
| btlejack | MITM attacks | Linux | Free | Advanced |
| bluepy | Python BLE | Linux | Free | Intermediate |
| Metasploit | Exploitation | Multi | Free | Advanced |

## Recommended Toolkit by Role

### Red Team / Penetration Tester
- HID Barcode Scanner (Android device)
- Ubertooth One or nRF52840
- Laptop with Kali Linux
- nRF Connect for Mobile
- Metasploit Framework
- Custom Python scripts

### Blue Team / Defender
- nRF Connect for Mobile (for testing)
- Wireshark
- Sysmon / auditd
- EDR solution
- SIEM for log analysis
- Bluetooth device management tools

### Security Researcher
- Multiple BLE adapters
- Ubertooth One
- nRF52840 DK
- Wireshark
- btlejack
- Software-defined radio (optional)
- Logic analyzer (for hardware analysis)

### Student / Beginner
- Android device with HID Scanner
- nRF Connect for Mobile
- Wireshark (free)
- Virtual machines (VirtualBox/VMware)
- Python with bluepy/bleak
- Online resources and tutorials

## Cost Breakdown

### Minimal Setup (~$50-100)
- Android phone (may already own)
- Computer (any laptop)
- Free software tools

### Intermediate Setup (~$200-400)
- Android device: $100-200
- nRF52840 Dongle: $10
- Bluetooth USB adapters: $20
- Software: Free

### Professional Setup ($500-1000+)
- High-end Android device: $300-500
- Ubertooth One: $120
- nRF52840 DK: $40
- Logic analyzer: $100
- Professional laptop: $800+
- Licenses (if applicable)
- Training courses

## Legal and Ethical Considerations

**Before using any tool**:
1. Ensure you have authorization
2. Use only on systems you own or have permission to test
3. Understand local laws regarding security testing
4. Consider joining bug bounty programs for legal testing

**Tool Usage Guidelines**:
- Packet sniffers: Only capture your own traffic or with permission
- MITM tools: Never use on production networks without authorization
- Exploitation frameworks: Only on authorized targets
- Always document your authorization

## Keeping Tools Updated

```bash
# Update Kali Linux tools
sudo apt update && sudo apt upgrade

# Update Python packages
pip3 list --outdated
pip3 install --upgrade <package>

# Update Metasploit
msfupdate

# Check for firmware updates on hardware tools
```

## Resources

- [Awesome Bluetooth Security](https://github.com/engn33r/awesome-bluetooth-security) - Curated list of BLE security resources
- [Bluetooth SIG](https://www.bluetooth.com/) - Official Bluetooth specifications
- [SecLists](https://github.com/danielmiessler/SecLists) - Security testing wordlists and payloads

---

[← Back to Wiki](../README.md)
