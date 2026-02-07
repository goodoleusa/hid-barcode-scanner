# Ethical Hacking Guidelines

## Overview

This module establishes the ethical, legal, and professional framework for conducting BLE security research and penetration testing. Understanding and adhering to these guidelines is not optional—it is fundamental to responsible security practice.

## Table of Contents

1. [Core Ethical Principles](#core-ethical-principles)
2. [Legal Framework](#legal-framework)
3. [Authorization and Scope](#authorization-and-scope)
4. [Responsible Disclosure](#responsible-disclosure)
5. [Professional Conduct](#professional-conduct)
6. [Research Ethics](#research-ethics)
7. [Incident Response](#incident-response)

## Core Ethical Principles

### The Hacker's Code of Ethics

1. **Do No Harm**
   - Never cause damage to systems or data
   - Minimize impact on availability and performance
   - Protect privacy of individuals
   - Consider consequences of your actions

2. **Respect Boundaries**
   - Only test authorized systems
   - Stay within defined scope
   - Stop immediately if you exceed authorization
   - Never access personal or sensitive data without explicit permission

3. **Transparency**
   - Be honest about capabilities and limitations
   - Document all actions taken
   - Report findings accurately and completely
   - Don't hide or minimize vulnerabilities

4. **Continuous Learning**
   - Stay current with technology and techniques
   - Share knowledge responsibly
   - Learn from mistakes
   - Contribute to the security community

5. **Professional Integrity**
   - Maintain confidentiality
   - Avoid conflicts of interest
   - Deliver quality work
   - Honor commitments

### Security Research vs. Malicious Hacking

| Ethical Security Research | Malicious Hacking |
|---------------------------|-------------------|
| ✅ Written authorization | ❌ No permission |
| ✅ Defined scope | ❌ Unrestricted access |
| ✅ Documented methodology | ❌ Hidden activities |
| ✅ Responsible disclosure | ❌ Public exploitation |
| ✅ Intent to improve security | ❌ Intent to harm/profit |
| ✅ Cooperation with owners | ❌ Adversarial relationship |
| ✅ Legal compliance | ❌ Criminal activity |

## Legal Framework

### Computer Fraud and Abuse Act (CFAA) - United States

**Key Provisions**:
- Prohibits unauthorized access to computers and networks
- Covers "protected computers" (most networked devices)
- Penalties include fines and imprisonment
- Intent matters, but "good intentions" are not a legal defense

**Relevant Sections**:
- 18 U.S.C. § 1030(a)(2): Unauthorized access to obtain information
- 18 U.S.C. § 1030(a)(5): Damage to computers
- 18 U.S.C. § 1030(a)(7): Extortion via computer access

**What This Means**:
- Don't test systems without explicit authorization
- "I was just testing security" is not a valid defense
- Academic research is not automatically protected
- Even accessing publicly visible systems without permission can be illegal

### International Laws

**European Union**:
- **Computer Misuse Act** (UK)
- **Network and Information Security (NIS) Directive**
- **GDPR** considerations for data handling

**Canada**:
- **Criminal Code Section 342.1**: Unauthorized use of computer

**Australia**:
- **Cybercrime Act 2001**

**Other Jurisdictions**:
Most countries have laws prohibiting unauthorized computer access. Research local laws before conducting any security testing.

### Safe Harbor and Legal Protections

**Bug Bounty Programs**:
- Provide legal safe harbor for security research
- Define scope and rules of engagement
- Offer protection from legal action (if rules followed)
- Examples: HackerOne, Bugcrowd, Synack

**Vulnerability Disclosure Policies**:
- Published by organizations
- Define how to report security issues
- Provide protection for researchers
- Specify what activities are permitted

**Legal Defense Strategies** (if questioned):
1. Show written authorization
2. Demonstrate limited scope
3. Provide documentation of methodology
4. Show responsible disclosure
5. Prove no data was accessed/stolen
6. Consult with attorney immediately

## Authorization and Scope

### Getting Proper Authorization

**Written Authorization Must Include**:

1. **Parties Involved**
   - Client organization (legal entity)
   - Testing organization/individual
   - Key contacts with authority to authorize

2. **Scope of Testing**
   - Specific systems/devices (IP addresses, MAC addresses, serial numbers)
   - Types of testing permitted (e.g., "BLE penetration testing", "HID injection")
   - Types of testing prohibited (e.g., "no DoS attacks", "no data exfiltration")
   - Physical locations covered

3. **Time Window**
   - Start date and time
   - End date and time
   - Specific hours if restricted (e.g., "off-business hours only")

4. **Constraints and Limitations**
   - Systems that must not be tested
   - Maximum impact allowed
   - Data handling restrictions
   - Third-party systems (usually prohibited)

5. **Reporting Requirements**
   - What to report
   - When to report (e.g., "immediately for critical findings")
   - How to report (secure channels)

6. **Legal Protections**
   - Hold harmless clause
   - Indemnification
   - Confidentiality agreement

**Sample Authorization Letter**:

```
SECURITY TESTING AUTHORIZATION

Date: [DATE]

TO: [TESTER NAME/ORGANIZATION]
FROM: [AUTHORIZED REPRESENTATIVE, TITLE]
ORGANIZATION: [CLIENT ORGANIZATION]

This letter authorizes [TESTER] to conduct security testing on behalf of [ORGANIZATION] under the following terms:

SCOPE:
- BLE devices: Smart locks in Building A (Serial Numbers: SL-001 through SL-050)
- Test workstations: 10.0.1.100 - 10.0.1.110
- Testing types: BLE reconnaissance, HID injection, access control testing

OUT OF SCOPE:
- Production servers
- Database systems
- Devices outside Building A
- Any systems not explicitly listed above

TIME PERIOD:
- Start: [DATE] at 18:00 (after business hours)
- End: [DATE] at 06:00 (before business hours)

CONSTRAINTS:
- No denial of service attacks
- No data exfiltration of real customer/employee data
- No modifications to production configurations
- Stop immediately if unintended impact occurs

REPORTING:
- Critical findings: Report immediately to [CONTACT]
- All findings: Formal report due [DATE]
- Secure communication via [ENCRYPTED EMAIL/PORTAL]

This authorization may be revoked at any time by [AUTHORIZED REPRESENTATIVE].

Signature: _________________________
Printed Name: [NAME]
Title: [TITLE]
Date: [DATE]

Tester Acknowledgment: _________________________
Date: [DATE]
```

### Scope Creep and Out-of-Scope Discoveries

**Scenario**: During authorized BLE testing, you discover an unrelated SQL injection vulnerability.

**Proper Response**:
1. **STOP** exploiting the out-of-scope vulnerability
2. **DOCUMENT** what you found (basic information only)
3. **NOTIFY** the client immediately
4. **REQUEST** additional authorization if client wants you to investigate
5. **NEVER** assume implied authorization

**Bad Response**:
- ❌ "I'm already authorized, so I'll just test this too"
- ❌ Exploiting the vulnerability to "prove" it exists
- ❌ Not reporting it because it's out of scope
- ❌ Sharing it publicly without permission

### Third-Party Systems

**Problem**: BLE devices often interact with cloud services, mobile apps, or other third-party systems.

**Rules**:
- **NEVER** test third-party systems without their explicit authorization
- If BLE device connects to cloud service, you generally cannot test the cloud service
- Mobile app testing may require separate authorization
- Vendor APIs are usually off-limits

**Example**:
You're authorized to test a smart lock. The lock connects to the manufacturer's cloud service.

✅ **You CAN test**:
- BLE communication between phone and lock
- Local lock vulnerabilities
- Authentication mechanisms on the lock itself

❌ **You CANNOT test** (without separate authorization):
- The manufacturer's cloud servers
- The manufacturer's API
- The mobile app (beyond local testing)
- Other customers' locks

## Responsible Disclosure

### The Disclosure Process

**Timeline** (typical):

1. **Day 0: Discovery**
   - Document vulnerability thoroughly
   - Assess severity and impact
   - Verify it's reproducible

2. **Day 1-7: Initial Contact**
   - Find appropriate contact (security@company.com)
   - Send initial notification
   - Provide high-level description (not full details yet)
   - Establish secure communication channel

3. **Day 7-14: Detailed Report**
   - Share complete technical details
   - Provide proof of concept (PoC)
   - Suggest remediation
   - Agree on disclosure timeline

4. **Day 14-90: Remediation Period**
   - Allow vendor time to fix
   - Typical timeline: 90 days
   - May extend for complex issues
   - Maintain communication

5. **Day 90+: Public Disclosure**
   - Publish findings after fix is available
   - Coordinate disclosure with vendor
   - Give users time to patch
   - Credit vendor for cooperation (if applicable)

### Disclosure Report Template

```markdown
# Vulnerability Disclosure Report

## Summary
- **Vulnerability**: [Brief description]
- **Affected Product**: [Product name, version]
- **Severity**: [Critical/High/Medium/Low]
- **Reporter**: [Your name, affiliation]
- **Date Discovered**: [DATE]

## Affected Systems
- Product: [Name]
- Versions: [Affected versions]
- Platforms: [Windows/Linux/Android/etc.]

## Vulnerability Details

### Description
[Detailed technical description]

### Attack Vector
[How the vulnerability can be exploited]

### Impact
[What an attacker can achieve]

### Proof of Concept
```
[Steps to reproduce]
[Code samples if applicable]
[Screenshots]
```

### Technical Details
- Vulnerability Type: [Buffer overflow, authentication bypass, etc.]
- Affected Component: [Specific code/feature]
- Root Cause: [Why the vulnerability exists]

## Remediation Recommendations

### Short-term Mitigations
1. [Immediate workarounds]
2. [Temporary fixes]

### Long-term Fix
1. [Recommended patch]
2. [Code changes needed]

## Timeline
- [DATE]: Vulnerability discovered
- [DATE]: Initial contact with vendor
- [DATE]: Detailed report sent
- [DATE]: Vendor acknowledgment
- [DATE]: Fix developed
- [DATE]: Fix released
- [DATE]: Public disclosure

## References
- [Related CVEs]
- [Similar vulnerabilities]
- [Industry standards]

## Contact
- Email: [secure email]
- PGP Key: [key fingerprint]
```

### Coordinated Disclosure vs. Full Disclosure

**Coordinated Disclosure** (Recommended):
- Work with vendor to fix before public disclosure
- Gives users time to patch
- Reduces harm
- Builds trust with vendors

**Full Disclosure** (Immediate public disclosure):
- Used when vendor is unresponsive
- When vulnerability is being actively exploited
- When fix is unreasonably delayed
- Last resort option

**Never**:
- Sell vulnerabilities to malicious actors
- Disclose without giving vendor chance to respond
- Threaten vendors to pressure them
- Exploit vulnerabilities for personal gain

## Professional Conduct

### Client Relationships

**Confidentiality**:
- All findings are confidential
- Never share client names without permission
- Don't discuss specifics publicly
- Secure all documentation

**Communication**:
- Professional at all times
- Clear, jargon-free reporting
- Prompt responses
- Manage expectations

**Quality Work**:
- Thorough testing
- Accurate reporting
- Actionable recommendations
- Follow-up support

### Handling Sensitive Data

**If you encounter sensitive data during testing**:

1. **Stop immediately**
   - Don't read, copy, or exfiltrate the data
   - Note that you found it, but don't examine it

2. **Notify client**
   - Inform them immediately
   - Describe what you found (without details)
   - Let them decide next steps

3. **Document minimally**
   - Note: "Found database with potentially sensitive data"
   - Don't include actual data in report
   - Use redaction if screenshots needed

4. **Delete any copies**
   - Remove any inadvertent copies
   - Clear browser cache, temporary files
   - Confirm deletion

**Examples of Sensitive Data**:
- Personally Identifiable Information (PII)
- Financial records
- Health information (HIPAA-protected)
- Authentication credentials
- Proprietary business information
- Communications (emails, messages)

### Conflicts of Interest

**Avoid**:
- Testing competitors of your employer/client
- Using information from one client to benefit another
- Financial interest in vulnerabilities you discover
- Testing systems where you have insider knowledge

**Disclose**:
- Previous relationships with client
- Financial interests in outcomes
- Personal relationships with employees
- Any potential bias

## Research Ethics

### Academic Research

**Requirements**:
- Institutional Review Board (IRB) approval (if involving human subjects)
- Informed consent from participants
- Ethical use of data
- Publication ethics

**Do's and Don'ts**:

✅ **Do**:
- Use lab/simulated environments
- Get IRB approval
- Publish methodology
- Share findings with affected parties
- Contribute to security community

❌ **Don't**:
- Test real users without consent
- Expose users to harm
- Publish before responsible disclosure
- Claim credit for others' work
- Withhold negative results

### Working with Vulnerable Populations

**Extra Care Required For**:
- Medical devices (patient safety)
- Critical infrastructure (public safety)
- Financial systems (economic harm)
- Government systems (national security)

**Additional Considerations**:
- Higher standard of care
- More conservative testing
- Extensive coordination
- Legal review
- Risk assessment

## Incident Response

### If Something Goes Wrong

**Immediate Steps**:

1. **Stop all testing activities**
2. **Assess the situation**
   - What happened?
   - What systems are affected?
   - Is there ongoing impact?

3. **Notify the client**
   - Call immediately (don't just email)
   - Explain what happened
   - Describe current impact
   - Offer assistance

4. **Document everything**
   - Timeline of events
   - Actions taken
   - Systems affected
   - Mitigations attempted

5. **Assist with remediation**
   - Help restore service
   - Provide technical expertise
   - Stay until resolved

**Post-Incident**:
- Conduct lessons learned
- Update methodology
- Improve risk assessment
- Consider if testing should continue

### Legal Issues

**If you're contacted by law enforcement**:

1. **Stay calm and professional**
2. **Don't lie, but don't volunteer information**
3. **Request to speak with attorney**
4. **Show your authorization letter**
5. **Document the interaction**
6. **Notify your client/employer**
7. **Follow attorney's advice**

**Have Ready**:
- Authorization documentation
- Scope definition
- Methodology documentation
- Communication records
- Legal counsel contact information

## Professional Organizations and Certifications

### Organizations

- **(ISC)² - International Information System Security Certification Consortium**
  - Code of Ethics for CISSPs
  
- **EC-Council**
  - Code of Ethics for CEH

- **ISACA**
  - Code of Professional Ethics

- **SANS/GIAC**
  - Professional Code of Conduct

### Certifications with Ethical Requirements

- **OSCP** (Offensive Security Certified Professional)
- **CEH** (Certified Ethical Hacker)
- **CISSP** (Certified Information Systems Security Professional)
- **GPEN** (GIAC Penetration Tester)

All require adherence to codes of ethics and can be revoked for violations.

## Case Studies

### Case Study 1: Marcus Hutchins (Good Outcome)

**Scenario**: Security researcher discovered and stopped WannaCry ransomware.

**Ethical Actions**:
- Researched malware to understand it
- Found kill switch domain
- Registered domain to stop spread
- Shared findings with community
- Worked with law enforcement

**Outcome**: Prevented massive damage, received recognition

**Lesson**: Ethical research and responsible disclosure save lives and systems.

### Case Study 2: Researcher Arrested (Bad Outcome)

**Scenario**: Researcher tested airport systems without authorization.

**Unethical Actions**:
- Tested systems without permission
- Assumed implied authorization
- Didn't notify owners before testing
- Publicized findings before disclosure

**Outcome**: Arrested, charged with federal crimes

**Lesson**: Good intentions don't override legal requirements. Always get authorization.

## Quiz

1. What are the three most important components of written authorization?
2. What should you do if you discover an out-of-scope vulnerability?
3. What is the typical timeline for responsible disclosure?
4. How should you handle accidentally encountering sensitive personal data?
5. Name three things that are illegal under the CFAA.
6. What is the difference between coordinated and full disclosure?
7. What should you do if something goes wrong during testing?
8. Why is a written authorization letter important?

## Ethical Pledge

**As a security professional, I pledge to**:

- Only test systems for which I have explicit written authorization
- Respect the privacy and safety of all individuals
- Report vulnerabilities responsibly and accurately
- Maintain confidentiality of all client information
- Stay within the defined scope of any engagement
- Stop immediately if I exceed my authorization
- Continuously improve my knowledge and skills
- Contribute positively to the security community
- Uphold the highest standards of professional conduct
- Use my skills to improve security, never to cause harm

**Signature**: _________________________ **Date**: _____________

---

**Keep this pledge visible while conducting security research. It represents your commitment to ethical practice.**

## Additional Resources

- [(ISC)² Code of Ethics](https://www.isc2.org/Ethics)
- [Bug Bounty Platforms](https://www.bugcrowd.com/hackers/bugcrowd-code/)
- [CFAA Full Text](https://www.justice.gov/jm/criminal-resource-manual-1030-computer-fraud-and-abuse-act)
- [Carnegie Mellon CERT Disclosure Guidelines](https://vuls.cert.org/confluence/display/CVD)
- [Google Project Zero Disclosure Policy](https://googleprojectzero.blogspot.com/p/vulnerability-disclosure-policy.html)

---

[← Back to Wiki Home](README.md)
