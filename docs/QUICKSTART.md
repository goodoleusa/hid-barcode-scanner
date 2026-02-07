# Quick Start Guide - BLE Cybersecurity Learning Wiki

## 🚀 Getting Started in 15 Minutes

This quick start guide will help you begin your BLE security learning journey immediately.

## Prerequisites Check

- [ ] Android device (Android 9+)
- [ ] Computer with Bluetooth
- [ ] Basic understanding of networking
- [ ] Willingness to learn!

## Setup Steps

### 1. Install HID Barcode Scanner (5 minutes)

**Option A: From GitHub (Recommended for this wiki)**
1. Visit [GitHub Releases](https://github.com/Fabi019/hid-barcode-scanner/releases)
2. Download latest APK
3. Install on Android device (enable "Install from unknown sources")

**Option B: From Play Store**
1. Search "HID Barcode Scanner" in Google Play
2. Install app by Fabi019

### 2. First Test - HID Mode (5 minutes)

1. **Open HID Barcode Scanner app**
2. **Go to Settings**:
   - Connection Mode: `HID Keyboard`
   - Keyboard Layout: Match your computer (e.g., `US`)
3. **Pair with Computer**:
   - Tap "Devices" in app
   - Tap "Scan for devices"
   - Select your computer
   - Accept pairing on both devices
4. **Test It**:
   - Open Notepad/TextEdit on computer
   - In HID Scanner app, go to Scanner
   - Type or scan: `HELLO WORLD`
   - Text should appear on computer!

✅ **Success?** You've completed your first HID input!

### 3. Basic Security Test (5 minutes)

**Test automatic command execution:**

1. **Configure Custom Key**:
   - Settings > Custom Keys > Add
   - Name: `Test Calc`
   - Keys: `{{GUI}}r{{DELAY 500}}calc{{ENTER}}`

2. **Test**:
   - Go to Scanner
   - Send the custom key
   - Calculator should open!

⚠️ **Important**: This demonstrates why HID attacks are powerful. Any keystroke can be injected!

## Your First Learning Path

### Beginner Track (Week 1)

**Day 1-2**: Understand the Basics
- Read: [Introduction to BLE Security](01-ble-security-introduction.md)
- Read: [Bluetooth HID Protocol](02-bluetooth-hid-protocol.md)
- Practice: Experiment with HID Scanner keyboard input

**Day 3-4**: Security Implications
- Read: [BLE Attack Vectors](03-ble-attack-vectors.md)
- Complete: [Exercise 1: HID Injection Basics](exercises/ex01-hid-injection-basics.md)

**Day 5-7**: Real-World Applications
- Read: [Using HID Scanner for Pentesting](04-hid-scanner-pentesting.md)
- Read: [Ethical Guidelines](10-ethical-guidelines.md) **← CRITICAL**
- Set up test environment

### Intermediate Track (Week 2-3)

**Focus Areas**:
- RFCOMM mode testing
- BLE access control analysis
- SMS-based MFA integration
- Complete exercises 2-4

**Resources**:
- [RFCOMM Advanced Testing](05-rfcomm-advanced-testing.md)
- [BLE Access Control](06-ble-access-control.md)
- [Secure SMS Proxy Integration](08-secure-sms-proxy-integration.md)

### Advanced Track (Week 4+)

**Focus Areas**:
- Digital locksmith techniques
- Custom payload development
- Building security test labs
- Complete all exercises

**Resources**:
- [Digital Locksmith](07-digital-locksmith.md)
- All exercises
- Build your own BLE devices

## Quick Command Reference

### HID Template Syntax

```
Modifier Keys:
{{CTRL}}      Control
{{ALT}}       Alt
{{GUI}}       Windows/Command key
{{SHIFT}}     Shift

Special Keys:
{{ENTER}}     Enter
{{TAB}}       Tab
{{ESC}}       Escape
{{DELAY 500}} Wait 500ms

Example:
{{GUI}}r{{DELAY 500}}notepad{{ENTER}}
Opens Notepad on Windows
```

### Python BLE Scanning

```python
from bluepy.btle import Scanner

scanner = Scanner()
devices = scanner.scan(10.0)

for dev in devices:
    print(f"{dev.addr} - {dev.getValueText(9)}")  # Name
```

### Windows Event Monitoring

```powershell
# Monitor for PowerShell execution
Get-WinEvent -FilterHashtable @{
    LogName='Security'; 
    Id=4688
} | Where-Object {$_.Message -like "*powershell*"}
```

## Essential Tools (Start With These)

### Free Software
- **nRF Connect** (Mobile): BLE scanning and exploration
- **Wireshark**: Packet analysis
- **Python with bluepy**: Scripting BLE interactions

### Optional Hardware (~$10-100)
- **nRF52840 Dongle** ($10): BLE development
- **Ubertooth One** ($120): Professional BLE sniffing

See [Tools Guide](reference/tools.md) for complete list.

## Common Issues & Solutions

### Issue: HID Scanner won't pair

**Solutions**:
1. Unpair from both sides completely
2. Restart Bluetooth on both devices
3. Try "Scan for devices" in app again
4. Check Android version (need 9+)

### Issue: Text appears garbled

**Solution**: Keyboard layout mismatch
- App setting must match computer's keyboard layout
- US layout ≠ UK layout ≠ German layout

### Issue: Commands don't execute

**Solution**: Delays too short
- Add longer delays: `{{DELAY 1000}}` (1 second)
- Fast computers need less delay, slow ones need more
- Test and adjust

### Issue: Computer doesn't see barcode scanner

**Solution**: Check connection mode
- For keyboard emulation: Use "HID Keyboard" mode
- For serial communication: Use "RFCOMM (SPP)" mode

## Practice Challenges

### Challenge 1: Safe HID Injection
**Task**: Create HID template that:
1. Opens calculator
2. Performs calculation: 123 + 456
3. Result should be 579

**Hint**: Use `{{DELAY}}` between keystrokes

### Challenge 2: Cross-Platform Payload
**Task**: Create single template that works on Windows, Linux, and macOS

**Hint**: Use applications available on all platforms (like browser)

### Challenge 3: Detection
**Task**: Set up monitoring to detect your HID injection

**Hint**: Use Sysmon (Windows) or auditd (Linux)

## What NOT to Do

❌ **Never test on systems you don't own or have explicit permission for**
❌ **Don't use these skills for unauthorized access**
❌ **Don't skip the ethical guidelines section**
❌ **Don't test on production systems**
❌ **Don't share credentials or sensitive data found during testing**

## Next Steps

### After This Quick Start:

1. **Read Ethical Guidelines** (30 minutes)
   - [Ethical Hacking Guidelines](10-ethical-guidelines.md)
   - This is NOT optional!

2. **Set Up Test Lab** (1-2 hours)
   - Create virtual machines
   - Install monitoring tools
   - Configure logging

3. **Complete Exercise 1** (2-3 hours)
   - [HID Injection Basics](exercises/ex01-hid-injection-basics.md)
   - Hands-on practice with guided steps

4. **Join Community**
   - GitHub Discussions
   - Security forums (r/netsec, r/AskNetsec)
   - Bug bounty platforms (for legal testing)

## Learning Resources

### Official Documentation
- [HID Scanner README](../README.md)
- [Bluetooth SIG Specifications](https://www.bluetooth.com/specifications/)

### Recommended Reading
- [Full Wiki Table of Contents](README.md)
- Start with modules 1-3
- Progress through exercises

### Video Tutorials (External)
- Search YouTube for "BLE security introduction"
- Look for talks from security conferences (DEF CON, Black Hat)

### Online Courses
- Offensive Security courses (OSCP path)
- SANS courses on wireless security
- Udemy/Coursera cybersecurity fundamentals

## Getting Help

### If You're Stuck:

1. **Check the Glossary**: [Glossary](reference/glossary.md)
2. **Review the Module**: Re-read relevant section
3. **Search GitHub Issues**: Someone may have had same problem
4. **Ask in Discussions**: Create a discussion post
5. **Consult Documentation**: Official HID Scanner docs

### When Asking for Help:

- Describe what you're trying to accomplish
- Show what you've tried
- Include error messages (if any)
- Specify your setup (Android version, computer OS, etc.)
- Confirm you have authorization for testing

## Important Reminders

### Legal
✅ Only test your own devices or with written permission
✅ Bug bounty programs provide legal safe harbor
✅ Understand laws in your jurisdiction

### Ethical
✅ Do no harm
✅ Respect privacy
✅ Responsible disclosure
✅ Continuous learning

### Professional
✅ Document everything
✅ Maintain confidentiality
✅ Deliver quality work
✅ Stay current with security trends

## Progress Checklist

Track your learning progress:

- [ ] Installed HID Barcode Scanner
- [ ] Successfully paired with computer
- [ ] Tested basic HID input
- [ ] Tested command execution (calc, notepad)
- [ ] Read Ethical Guidelines (REQUIRED)
- [ ] Read BLE Security Introduction
- [ ] Read Bluetooth HID Protocol
- [ ] Completed Exercise 1
- [ ] Set up test lab
- [ ] Read BLE Attack Vectors
- [ ] Tested RFCOMM mode
- [ ] Completed Exercise 4 (SMS Auth)
- [ ] Built a BLE security project
- [ ] All exercises completed

## Time Commitment Estimates

- **Quick Start**: 15 minutes
- **Module 1-2**: 2-3 hours
- **Exercise 1**: 2-3 hours
- **Beginner Track**: 10-15 hours
- **Intermediate Track**: 20-30 hours
- **Advanced Track**: 40+ hours
- **Complete Curriculum**: 60-100 hours

Take your time! Quality learning is better than rushing.

## Ready to Begin?

🎓 **Start Here**: [Introduction to BLE Security →](01-ble-security-introduction.md)

Or jump to:
- 📖 [Full Wiki Table of Contents](README.md)
- 🛠️ [Tools Guide](reference/tools.md)
- 📝 [Glossary](reference/glossary.md)
- ⚖️ [Ethical Guidelines](10-ethical-guidelines.md)

---

**Remember**: With great power comes great responsibility. Use this knowledge ethically and legally.

Happy learning! 🚀🔒
