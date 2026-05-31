# Lab 5 - Username enumeration via response timing

## 1. Lab Name

Username enumeration via response timing

## 2. Vulnerability Type

Username Enumeration / Authentication Flaw

## 3. Lab Objective

To identify a valid username using response timing differences and brute-force the password.

## 4. Target Functionality

Login functionality:

`POST /login`

## 5. Vulnerable Endpoint

`POST /login`

## 6. Payloads Used

### Username Enumeration

```http
POST /login

username=<payload>&password=testtesttesttesttesttesttesttest
```

### Rate Limit Bypass

```http
X-Forwarded-For: 1
```

### Password Brute Force

```http
POST /login

username=accounting&password=<payload>
```

## 7. Exploitation Steps

### Phase 1 - Initial Analysis

1. Open login page.
2. Enter invalid credentials.
3. Capture request using Burp Suite.
4. Send request to Repeater.
5. Observe response behavior.

---

### Phase 2 - Rate Limit Bypass

1. Multiple invalid login attempts triggered rate limiting.
2. Application responded with:

```text
You have made too many incorrect login attempts.
Please try again in 30 minute(s).
```

3. Add header:

```http
X-Forwarded-For: 1
```

4. Increment header value for each request.
5. Successfully bypass rate limiting.

---

### Phase 3 - Username Enumeration

1. Send login request to Intruder.
2. Configure Pitchfork attack.
3. Use username wordlist.
4. Use long password payload.
5. Start attack.
6. Sort results by response time.
7. Observe one response significantly slower than others.
8. Identify valid username:

```text
accounting
```

---

### Phase 4 - Password Brute Force

1. Send request again to Intruder.
2. Set:

```text
username=accounting
```

3. Select password parameter as payload position.
4. Load password wordlist.
5. Start attack.
6. Observe successful response:

```http
HTTP/2 302 Found
Location: /my-account?id=accounting
```

7. Valid credentials identified:

```text
username: accounting
password: 123qwe
```

8. Login successful.
9. Lab solved.

## 8. Proof of Exploit

* Valid username discovered through response timing differences
* Rate limiting bypassed successfully
* Password brute-forced successfully
* Authenticated as target user

![Exploit Screenshot](images/lab5-1.png)
![Exploit Screenshot](images/lab5-2.png)
![Exploit Screenshot](images/lab5-3.png)
![Exploit Screenshot](images/lab5-4.png)
![Exploit Screenshot](images/lab5-5.png)
![Exploit Screenshot](images/lab5-6.png)
![Exploit Screenshot](images/lab5-7.png)
![Exploit Screenshot](images/lab5-8.png)
![Exploit Screenshot](images/lab5-9.png)
![Exploit Screenshot](images/lab5-10.png)
![Exploit Screenshot](images/lab5-11.png)
![Exploit Screenshot](images/lab5-12.png)
![Exploit Screenshot](images/lab5-13.png)
![Exploit Screenshot](images/lab5-14.png)
![Exploit Screenshot](images/lab5-15.png)
![Exploit Screenshot](images/lab5-16.png)
![Exploit Screenshot](images/lab5-17.png)
![Exploit Screenshot](images/lab5-18.png)
![Exploit Screenshot](images/lab5-19.png)
![Exploit Screenshot](images/lab5-20.png)
![Exploit Screenshot](images/lab5-21.png)
![Exploit Screenshot](images/lab5-22.png)

## 9. Impact

* Username disclosure
* Credential brute forcing
* Unauthorized account access
* Authentication security bypass

## 10. Root Cause

Application processes valid usernames differently, causing measurable response timing differences that allow account enumeration.

Additionally, rate limiting relies on user-controlled headers.

## 11. Mitigation / Fix

* Normalize authentication response timings
* Use generic authentication responses
* Implement proper server-side rate limiting
* Ignore untrusted X-Forwarded-For headers
* Enable account lockout mechanisms
* Use MFA protection

## 12. OWASP Mapping

OWASP Top 10 – A07: Identification and Authentication Failures
