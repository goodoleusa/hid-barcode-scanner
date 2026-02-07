# Exercise 4: SMS-Based Authentication & Secure SMS Proxy

## Overview

This exercise explores implementing SMS-based multi-factor authentication (MFA) using Secure SMS Proxy, integrating it with BLE access control systems, and understanding its security implications through hands-on pentesting.

## Learning Objectives

- Implement SMS-based MFA system
- Configure and secure Secure SMS Proxy
- Integrate SMS authentication with BLE access control
- Identify SMS MFA vulnerabilities
- Test SMS-based authentication security
- Implement proper defensive measures

## Prerequisites

### Hardware
- Android device (Android 5.0+) for Secure SMS Proxy
- Computer or Raspberry Pi for server
- Android device with HID Barcode Scanner
- (Optional) BLE development board for smart lock simulation

### Software
- Secure SMS Proxy app: [Download](https://github.com/frimtec/secure-sms-proxy)
- Python 3.8+ with requests, flask libraries
- HID Barcode Scanner app
- (Optional) Node.js for alternative implementation

### Knowledge
- Basic Python programming
- Understanding of HTTP/REST APIs
- BLE fundamentals (from previous exercises)
- Basic understanding of MFA concepts

## Lab Setup

### Part 1: Install Secure SMS Proxy

**1.1 Download and Install**

On Android device:

1. Download APK from [GitHub Releases](https://github.com/frimtec/secure-sms-proxy/releases)
2. Install APK (enable "Install from unknown sources")
3. Open Secure SMS Proxy

**1.2 Initial Configuration**

1. **Create API Key**:
   - Go to Settings > API Keys
   - Tap "Generate New Key"
   - Copy and save securely: `your-api-key-here`

2. **Configure Network**:
   - Settings > Network
   - Note device IP address: `192.168.1.XXX`
   - Enable HTTPS (recommended)
   - Set port: `8080`

3. **Registration Rules** (optional):
   - Settings > Rules
   - Add rule to filter which SMS to forward
   - Pattern: `.*Code.*` (forward SMS containing "Code")

4. **Test Configuration**:
   - Send test SMS to device: "Test Code 123456"
   - Check Secure SMS Proxy logs
   - Verify SMS is captured

### Part 2: Set Up Server

**2.1 Install Dependencies**

```bash
# Create project directory
mkdir sms-auth-lab
cd sms-auth-lab

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install requirements
pip install flask requests python-dotenv pyjwt
```

**2.2 Create Environment Configuration**

```bash
# Create .env file
nano .env
```

Add:
```env
SMS_PROXY_URL=https://192.168.1.XXX:8080
SMS_PROXY_API_KEY=your-api-key-here
SECRET_KEY=your-flask-secret-key
JWT_SECRET=your-jwt-secret-key
```

**Security Note**: Use strong random keys in production. Generate with:
```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

## Exercise Tasks

### Task 1: Basic SMS Sending

**Objective**: Send SMS messages via Secure SMS Proxy API.

**1.1 Create SMS Sender**

Create `sms_sender.py`:

```python
import requests
import os
from dotenv import load_dotenv

load_dotenv()

class SMSSender:
    def __init__(self):
        self.base_url = os.getenv('SMS_PROXY_URL')
        self.api_key = os.getenv('SMS_PROXY_API_KEY')
        self.headers = {
            'Authorization': f'Bearer {self.api_key}',
            'Content-Type': 'application/json'
        }
    
    def send_sms(self, phone_number, message):
        """Send SMS via Secure SMS Proxy"""
        url = f"{self.base_url}/api/send"
        
        payload = {
            'phoneNumber': phone_number,
            'message': message
        }
        
        try:
            response = requests.post(
                url,
                json=payload,
                headers=self.headers,
                verify=True,  # Verify TLS certificate
                timeout=10
            )
            
            if response.status_code == 200:
                print(f"✓ SMS sent successfully to {phone_number}")
                return True
            else:
                print(f"✗ Failed to send SMS: {response.status_code}")
                print(f"  Response: {response.text}")
                return False
                
        except requests.exceptions.RequestException as e:
            print(f"✗ Error sending SMS: {e}")
            return False
    
    def get_status(self):
        """Check Secure SMS Proxy status"""
        url = f"{self.base_url}/api/status"
        
        try:
            response = requests.get(
                url,
                headers=self.headers,
                verify=True,
                timeout=5
            )
            
            if response.status_code == 200:
                return response.json()
            else:
                return None
                
        except requests.exceptions.RequestException as e:
            print(f"✗ Error checking status: {e}")
            return None

# Test the SMS sender
if __name__ == "__main__":
    sender = SMSSender()
    
    # Check status
    status = sender.get_status()
    if status:
        print(f"Proxy Status: {status}")
    
    # Send test SMS (use your own phone number!)
    test_phone = input("Enter your phone number (E.164 format, e.g., +1234567890): ")
    sender.send_sms(test_phone, "Test message from SMS Auth Lab")
```

**1.2 Run and Test**

```bash
python sms_sender.py
```

**Expected Result**: You should receive an SMS on the test phone number.

**Troubleshooting**:
- If SSL error: Check `SMS_PROXY_URL` (http vs https)
- If 401 Unauthorized: Check `SMS_PROXY_API_KEY`
- If timeout: Check network connectivity, firewall settings

### Task 2: Implement SMS MFA System

**Objective**: Build a complete SMS-based authentication system.

**2.1 Create Authentication Server**

Create `auth_server.py`:

```python
from flask import Flask, request, jsonify
import secrets
import time
from datetime import datetime, timedelta
from sms_sender import SMSSender

app = Flask(__name__)
sms_sender = SMSSender()

# In-memory storage (use database in production!)
pending_codes = {}  # {phone_number: {"code": "123456", "expires": timestamp, "attempts": 0}}
authenticated_sessions = {}  # {session_token: {"phone": "+123", "expires": timestamp}}

def generate_code():
    """Generate 6-digit verification code"""
    return f"{secrets.randbelow(1000000):06d}"

def generate_session_token():
    """Generate secure session token"""
    return secrets.token_urlsafe(32)

@app.route('/api/auth/request', methods=['POST'])
def request_auth():
    """Request SMS authentication code"""
    data = request.json
    phone_number = data.get('phone_number')
    
    if not phone_number:
        return jsonify({'error': 'Phone number required'}), 400
    
    # Validate phone number format (basic validation)
    if not phone_number.startswith('+') or len(phone_number) < 10:
        return jsonify({'error': 'Invalid phone number format'}), 400
    
    # Generate verification code
    code = generate_code()
    expires = time.time() + 300  # 5 minutes
    
    # Store pending code
    pending_codes[phone_number] = {
        'code': code,
        'expires': expires,
        'attempts': 0
    }
    
    # Send SMS
    message = f"Your verification code is: {code}\\nValid for 5 minutes.\\nDo not share this code."
    
    if sms_sender.send_sms(phone_number, message):
        return jsonify({
            'status': 'success',
            'message': 'Verification code sent',
            'expires_in': 300
        }), 200
    else:
        return jsonify({'error': 'Failed to send SMS'}), 500

@app.route('/api/auth/verify', methods=['POST'])
def verify_auth():
    """Verify SMS code and create session"""
    data = request.json
    phone_number = data.get('phone_number')
    code = data.get('code')
    
    if not phone_number or not code:
        return jsonify({'error': 'Phone number and code required'}), 400
    
    # Check if code exists
    if phone_number not in pending_codes:
        return jsonify({'error': 'No pending verification'}), 404
    
    stored = pending_codes[phone_number]
    
    # Check expiration
    if time.time() > stored['expires']:
        del pending_codes[phone_number]
        return jsonify({'error': 'Code expired'}), 401
    
    # Check attempts (prevent brute force)
    if stored['attempts'] >= 3:
        del pending_codes[phone_number]
        return jsonify({'error': 'Too many attempts. Request new code.'}), 429
    
    # Verify code
    if code == stored['code']:
        # Success! Create session
        session_token = generate_session_token()
        session_expires = time.time() + 3600  # 1 hour
        
        authenticated_sessions[session_token] = {
            'phone': phone_number,
            'expires': session_expires
        }
        
        # Clean up pending code
        del pending_codes[phone_number]
        
        return jsonify({
            'status': 'success',
            'session_token': session_token,
            'expires_in': 3600
        }), 200
    else:
        # Failed attempt
        stored['attempts'] += 1
        remaining = 3 - stored['attempts']
        
        return jsonify({
            'error': 'Invalid code',
            'remaining_attempts': remaining
        }), 401

@app.route('/api/auth/check', methods=['GET'])
def check_auth():
    """Check if session is valid"""
    auth_header = request.headers.get('Authorization')
    
    if not auth_header or not auth_header.startswith('Bearer '):
        return jsonify({'error': 'No authorization provided'}), 401
    
    session_token = auth_header[7:]  # Remove "Bearer "
    
    if session_token not in authenticated_sessions:
        return jsonify({'error': 'Invalid session'}), 401
    
    session = authenticated_sessions[session_token]
    
    if time.time() > session['expires']:
        del authenticated_sessions[session_token]
        return jsonify({'error': 'Session expired'}), 401
    
    return jsonify({
        'status': 'authenticated',
        'phone': session['phone'],
        'expires_in': int(session['expires'] - time.time())
    }), 200

@app.route('/api/auth/logout', methods=['POST'])
def logout():
    """Invalidate session"""
    auth_header = request.headers.get('Authorization')
    
    if auth_header and auth_header.startswith('Bearer '):
        session_token = auth_header[7:]
        if session_token in authenticated_sessions:
            del authenticated_sessions[session_token]
    
    return jsonify({'status': 'logged out'}), 200

if __name__ == '__main__':
    # For testing only! Use proper WSGI server in production
    app.run(host='0.0.0.0', port=5000, debug=True)
```

**2.2 Run Auth Server**

```bash
python auth_server.py
```

Server will start on `http://localhost:5000`

**2.3 Test Authentication Flow**

Create `test_auth.py`:

```python
import requests
import time

BASE_URL = "http://localhost:5000"

def test_authentication():
    phone = input("Enter your phone number (+1234567890): ")
    
    # Step 1: Request verification code
    print("\\n[1] Requesting verification code...")
    response = requests.post(f"{BASE_URL}/api/auth/request", json={
        'phone_number': phone
    })
    
    if response.status_code == 200:
        print("✓ Code sent successfully")
        print(f"  Response: {response.json()}")
    else:
        print(f"✗ Failed: {response.text}")
        return
    
    # Step 2: Enter code
    code = input("\\nEnter the code you received via SMS: ")
    
    print("\\n[2] Verifying code...")
    response = requests.post(f"{BASE_URL}/api/auth/verify", json={
        'phone_number': phone,
        'code': code
    })
    
    if response.status_code == 200:
        data = response.json()
        print("✓ Authentication successful!")
        print(f"  Session token: {data['session_token'][:20]}...")
        session_token = data['session_token']
    else:
        print(f"✗ Verification failed: {response.text}")
        return
    
    # Step 3: Check authentication
    print("\\n[3] Checking session validity...")
    response = requests.get(f"{BASE_URL}/api/auth/check", headers={
        'Authorization': f'Bearer {session_token}'
    })
    
    if response.status_code == 200:
        print("✓ Session is valid")
        print(f"  Details: {response.json()}")
    else:
        print(f"✗ Session invalid: {response.text}")
    
    # Step 4: Logout
    input("\\nPress Enter to logout...")
    response = requests.post(f"{BASE_URL}/api/auth/logout", headers={
        'Authorization': f'Bearer {session_token}'
    })
    print("✓ Logged out")

if __name__ == "__main__":
    test_authentication()
```

Run:
```bash
python test_auth.py
```

**Expected Flow**:
1. Enter phone number
2. Receive SMS with code
3. Enter code
4. Get session token
5. Verify session is valid
6. Logout

### Task 3: BLE + SMS Integration

**Objective**: Integrate SMS MFA with BLE access control.

**3.1 Create BLE Access Controller**

Create `ble_access_control.py`:

```python
import requests
from sms_sender import SMSSender

class BLEAccessControl:
    def __init__(self, auth_server_url):
        self.auth_server = auth_server_url
        self.sms_sender = SMSSender()
        self.access_log = []
    
    def request_access(self, device_id, user_id, phone_number):
        """
        Simulates BLE access request that triggers SMS MFA
        
        In real implementation:
        - device_id: BLE MAC address of requesting device
        - user_id: User identifier (from initial BLE handshake)
        - phone_number: Associated phone number (from user database)
        """
        print(f"\\n[BLE Access Control] Access requested")
        print(f"  Device: {device_id}")
        print(f"  User: {user_id}")
        print(f"  Phone: {phone_number}")
        
        # Step 1: Request SMS verification
        print("\\n[1] Sending SMS verification...")
        response = requests.post(f"{self.auth_server}/api/auth/request", json={
            'phone_number': phone_number
        })
        
        if response.status_code != 200:
            self.log_access(device_id, user_id, "SMS send failed")
            return {'status': 'denied', 'reason': 'SMS send failed'}
        
        print("✓ SMS sent. Waiting for user to verify...")
        
        # Step 2: Return pending status (app must call verify_access)
        return {
            'status': 'pending',
            'message': 'Enter SMS code to complete access',
            'device_id': device_id
        }
    
    def verify_access(self, device_id, phone_number, sms_code):
        """
        Verify SMS code and grant access
        """
        print(f"\\n[BLE Access Control] Verifying code for {device_id}...")
        
        # Verify code with auth server
        response = requests.post(f"{self.auth_server}/api/auth/verify", json={
            'phone_number': phone_number,
            'code': sms_code
        })
        
        if response.status_code == 200:
            session_data = response.json()
            print("✓ Code verified successfully")
            
            # Grant access
            self.log_access(device_id, phone_number, "access granted")
            self.actuate_lock(device_id, "unlock")
            
            return {
                'status': 'granted',
                'session_token': session_data['session_token'],
                'message': 'Access granted. Lock unlocked.'
            }
        else:
            self.log_access(device_id, phone_number, "invalid code")
            return {
                'status': 'denied',
                'reason': response.json().get('error', 'Verification failed')
            }
    
    def actuate_lock(self, device_id, action):
        """
        Simulate lock actuation
        In real implementation, this would send BLE command to smart lock
        """
        print(f"\\n[Lock Actuation] {action.upper()} command sent to {device_id}")
        # In reality: send BLE GATT write to unlock characteristic
        # See Exercise 3 for actual BLE communication code
    
    def log_access(self, device_id, user, result):
        """Log access attempt"""
        import datetime
        log_entry = {
            'timestamp': datetime.datetime.now().isoformat(),
            'device_id': device_id,
            'user': user,
            'result': result
        }
        self.access_log.append(log_entry)
        print(f"[Access Log] {log_entry}")

# Test the integration
if __name__ == "__main__":
    access_control = BLEAccessControl("http://localhost:5000")
    
    # Simulate BLE access request
    device_id = "AA:BB:CC:DD:EE:FF"
    user_id = "john_doe"
    phone = input("Enter phone number for testing: ")
    
    # Request access
    result = access_control.request_access(device_id, user_id, phone)
    
    if result['status'] == 'pending':
        # User receives SMS, enters code
        sms_code = input("\\nEnter SMS code: ")
        
        # Verify and grant access
        verify_result = access_control.verify_access(device_id, phone, sms_code)
        print(f"\\nFinal result: {verify_result}")
    else:
        print(f"\\nAccess denied: {result}")
    
    # Show access log
    print("\\n=== Access Log ===")
    for entry in access_control.access_log:
        print(entry)
```

Run:
```bash
python ble_access_control.py
```

### Task 4: Security Testing

**Objective**: Identify vulnerabilities in SMS-based authentication.

**4.1 Test Rate Limiting**

Create `security_tests.py`:

```python
import requests
import time

BASE_URL = "http://localhost:5000"

def test_rate_limiting():
    """Test if system has rate limiting on SMS requests"""
    print("[Test] Rate Limiting on SMS Requests\\n")
    
    phone = "+1234567890"  # Test phone
    
    success_count = 0
    for i in range(10):
        response = requests.post(f"{BASE_URL}/api/auth/request", json={
            'phone_number': phone
        })
        
        print(f"Attempt {i+1}: Status {response.status_code}")
        
        if response.status_code == 200:
            success_count += 1
        elif response.status_code == 429:  # Too Many Requests
            print("✓ Rate limiting detected!")
            break
        
        time.sleep(0.5)
    
    if success_count >= 10:
        print("✗ WARNING: No rate limiting detected!")
        print("  Attacker could spam SMS messages")
    
    print(f"\\nResult: {success_count}/10 requests succeeded\\n")

def test_code_brute_force():
    """Test if system prevents brute force on verification codes"""
    print("[Test] Brute Force Protection on Codes\\n")
    
    phone = "+1234567890"
    
    # Request code first
    response = requests.post(f"{BASE_URL}/api/auth/request", json={
        'phone_number': phone
    })
    
    if response.status_code != 200:
        print("Failed to request code")
        return
    
    # Try brute forcing (system should lock after 3 attempts)
    for attempt in range(1, 6):
        fake_code = f"{attempt:06d}"
        
        response = requests.post(f"{BASE_URL}/api/auth/verify", json={
            'phone_number': phone,
            'code': fake_code
        })
        
        print(f"Attempt {attempt} (code: {fake_code}): {response.status_code}")
        
        if response.status_code == 429:
            print("✓ Brute force protection activated!")
            break
    
    print()

def test_code_expiration():
    """Test if codes expire properly"""
    print("[Test] Code Expiration\\n")
    
    phone = "+1234567890"
    
    # Request code
    response = requests.post(f"{BASE_URL}/api/auth/request", json={
        'phone_number': phone
    })
    
    if response.status_code == 200:
        print("✓ Code requested")
        print("  Waiting 6 minutes for expiration...")
        print("  (For faster testing, modify auth_server.py to use shorter expiration)")
        
        # In real test, wait 6 minutes
        # time.sleep(360)
        
        print("\\n  To test: Try verifying with code after 6 minutes")
        print("  Expected: 'Code expired' error")
    
    print()

def test_session_security():
    """Test session token security"""
    print("[Test] Session Token Security\\n")
    
    # Try accessing protected endpoint without token
    print("1. Access without token:")
    response = requests.get(f"{BASE_URL}/api/auth/check")
    print(f"   Status: {response.status_code}")
    
    if response.status_code == 401:
        print("   ✓ Properly rejects unauthenticated requests")
    else:
        print("   ✗ WARNING: Allows access without authentication!")
    
    # Try with invalid token
    print("\\n2. Access with invalid token:")
    response = requests.get(f"{BASE_URL}/api/auth/check", headers={
        'Authorization': 'Bearer invalid-token-12345'
    })
    print(f"   Status: {response.status_code}")
    
    if response.status_code == 401:
        print("   ✓ Properly rejects invalid tokens")
    else:
        print("   ✗ WARNING: Accepts invalid tokens!")
    
    print()

def run_all_tests():
    """Run all security tests"""
    print("="*50)
    print("SMS Authentication Security Tests")
    print("="*50)
    print()
    
    test_rate_limiting()
    test_code_brute_force()
    test_code_expiration()
    test_session_security()
    
    print("="*50)
    print("Tests Complete")
    print("="*50)

if __name__ == "__main__":
    # Make sure auth_server.py is running!
    run_all_tests()
```

Run:
```bash
python security_tests.py
```

**Expected Results**:
- Rate limiting should prevent SMS spam
- Brute force protection should lock after 3 attempts
- Expired codes should be rejected
- Invalid/missing tokens should be rejected

**4.2 Identify Vulnerabilities**

Document findings:

| Vulnerability | Severity | Description | Mitigation |
|---------------|----------|-------------|------------|
| SMS Interception | High | SMS can be intercepted via SS7 attacks | Use TOTP instead, add push notification |
| SIM Swapping | High | Attacker can swap SIM card | Require additional verification, monitor for SIM changes |
| No Rate Limiting | Medium | Can spam SMS requests | Implement rate limiting per phone/IP |
| Weak Code Entropy | Medium | 6-digit code (1M possibilities) | Increase to 8 digits, shorter expiration |
| Session Hijacking | Medium | If token is stolen, full access | Short session expiration, IP binding |

### Task 5: Improved Implementation

**Objective**: Implement additional security measures.

**5.1 Add Rate Limiting**

Modify `auth_server.py` to add rate limiting:

```python
from collections import defaultdict
import time

# Add these global variables
request_counts = defaultdict(list)  # {ip_address: [timestamp1, timestamp2, ...]}

def check_rate_limit(ip_address, max_requests=5, window=60):
    """
    Check if IP has exceeded rate limit
    max_requests: Maximum requests allowed
    window: Time window in seconds
    """
    now = time.time()
    
    # Clean old timestamps
    request_counts[ip_address] = [
        ts for ts in request_counts[ip_address]
        if now - ts < window
    ]
    
    # Check if limit exceeded
    if len(request_counts[ip_address]) >= max_requests:
        return False
    
    # Add current request
    request_counts[ip_address].append(now)
    return True

# Modify request_auth function:
@app.route('/api/auth/request', methods=['POST'])
def request_auth():
    # Add rate limiting
    client_ip = request.remote_addr
    if not check_rate_limit(client_ip):
        return jsonify({'error': 'Rate limit exceeded. Try again later.'}), 429
    
    # ... rest of function
```

**5.2 Add Phone Number Verification**

```python
import re

def validate_phone_number(phone):
    """Validate phone number format"""
    # E.164 format: +[country code][number]
    pattern = r'^\+[1-9]\d{1,14}$'
    return re.match(pattern, phone) is not None

# Use in request_auth:
if not validate_phone_number(phone_number):
    return jsonify({'error': 'Invalid phone number format'}), 400
```

**5.3 Add Audit Logging**

```python
import logging

# Configure logging
logging.basicConfig(
    filename='auth_audit.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

def audit_log(event, details):
    """Log security-relevant events"""
    logging.info(f"{event}: {details}")

# Add to request_auth:
audit_log('SMS_REQUESTED', {
    'phone': phone_number,
    'ip': request.remote_addr
})

# Add to verify_auth:
audit_log('VERIFICATION_ATTEMPT', {
    'phone': phone_number,
    'success': (code == stored['code']),
    'ip': request.remote_addr
})
```

## Challenge Tasks

### Challenge 1: SIM Swap Detection

Implement detection for potential SIM swap attacks:

1. Track device identifiers with phone numbers
2. Alert if phone number suddenly connects from different device
3. Require additional verification

**Hint**: Store device fingerprint (user agent, IP, etc.) with phone number.

### Challenge 2: Backup Authentication

Implement fallback authentication when SMS fails:

1. TOTP (Time-based One-Time Password)
2. Backup codes
3. Email verification

### Challenge 3: Complete Integration

Build fully functional BLE smart lock with SMS MFA:

1. Use ESP32/nRF52 with servo/motor
2. Implement BLE communication
3. Integrate SMS authentication
4. Add HID Barcode Scanner for code input
5. Create mobile app for control

## Assessment

### Deliverables

1. **Working Implementation** (code)
   - SMS authentication server
   - BLE integration code
   - Security tests

2. **Security Analysis Report** (2-3 pages)
   - Identified vulnerabilities
   - Test results
   - Risk assessment
   - Mitigation recommendations

3. **Architecture Diagram**
   - Show all components
   - Data flow
   - Security boundaries

4. **Demo Video** (3-5 minutes)
   - Show working system
   - Demonstrate SMS MFA flow
   - Show security tests

### Evaluation Criteria

- **Functionality** (25%): Does the system work correctly?
- **Security** (35%): Are security measures properly implemented?
- **Code Quality** (15%): Clean, documented code?
- **Testing** (15%): Thorough security testing?
- **Documentation** (10%): Clear and comprehensive?

## Resources

- [Secure SMS Proxy Documentation](https://github.com/frimtec/secure-sms-proxy/blob/master/README.md)
- [NIST SP 800-63B: Authentication Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [SMS Security Best Practices](https://www.nist.gov/blogs/cybersecurity-insights/back-basics-multi-factor-authentication)

## Next Steps

- [Exercise 5: Digital Lock Analysis →](ex05-digital-lock-analysis.md)
- [Review: Secure SMS Proxy Integration](../08-secure-sms-proxy-integration.md)

---

[← Back to Exercises](../README.md#hands-on-exercises) | [Next Exercise →](ex05-digital-lock-analysis.md)
