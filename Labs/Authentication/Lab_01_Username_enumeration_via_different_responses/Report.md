# AUTH-001 - Username Enumeration via Different Responses

## Report Information

- **Category:** Authentication
- **Subcategory:** Username Enumeration
- **Severity:** Medium

---

## Executive Summary

The application exposes different authentication responses depending on whether the supplied username exists.

By comparing authentication responses during login attempts, an attacker can enumerate valid usernames before launching password attacks.

After identifying a valid username, a password brute-force attack was performed against that account, resulting in successful authentication and full access to the victim's account.

This issue significantly increases the effectiveness of credential-based attacks such as password brute-force, password spraying, and credential stuffing.

---

## Affected Components

- Login functionality
- Authentication mechanism
- User authentication workflow

---

## Vulnerability Description

The application returns different responses based on the validity of the supplied username.

When an invalid username is submitted, the server responds with:

```
Invalid username
```

However, when a valid username is supplied with an incorrect password, the response changes to:

```
Incorrect password
```

This behavioral difference allows attackers to distinguish valid usernames from invalid ones without possessing valid credentials.

Once a valid username is identified, attackers can focus password attacks on existing accounts instead of guessing both usernames and passwords simultaneously.

---

## Proof of Concept (PoC)

### Step 1 – Observe the Default Authentication Response

Submit a login request using an invalid username.

The application responds with:

```
Invalid username
```

**Screenshot 1:** Invalid Username Response.

![Step 1](Screenshots/01_Invalid_Username_Response.png)

---

### Step 2 – Enumerate Valid Usernames

Using Burp Intruder, submit login requests with usernames from the provided username list while keeping the password constant.

Most responses return:

- HTTP Status: **200**
- Response Length: **3352**

One response returns:

- HTTP Status: **200**
- Response Length: **3354**

indicating a behavioral difference.

**Screenshot 2:** Username Enumeration.

![Step 2](Screenshots/02_Username_Enumeration.png)

---

### Step 3 – Verify the Valid Username

Replay the identified request in Burp Repeater.

Instead of returning:

```
Invalid username
```

the application responds with:

```
Incorrect password
```

confirming that the username exists.

**Screenshot 3:** Valid Username Confirmation.

![Step 3](Screenshots/03_Valid_Username_Confirmation.png)

---

### Step 4 – Perform Password Brute Force

Fix the discovered username and use Burp Intruder with the provided password list.

One request returns:

- HTTP Status: **302**
- Response Length: **188**

indicating successful authentication.

**Screenshot 4:** Password Brute Force.

![Step 4](Screenshots/04_Password_Brute_Force.png)

---

### Step 5 – Inspect the Successful Authentication Response

Replay the successful request in Burp Repeater.

The server responds with:

- HTTP 302 Found
- Session Cookie
- Redirect to:

```
/my-account?id=oracle
```

confirming successful authentication.

**Screenshot 5:** Successful Login Response.

![Step 5](Screenshots/05_Successful_Login_Response.png)

---

### Step 6 – Verify Authenticated Access

Reuse the issued session cookie and access the account page.

The application successfully authenticates the session and displays the victim user's account.

**Screenshot 6:** Authenticated Account.

![Step 6](Screenshots/06_Authenticated_Account.png)

---

### Step 7 – Verify Lab Completion

The lab is successfully completed after authenticating as the victim user.

**Screenshot 7:** Lab Completed.

![Step 7](Screenshots/07_Lab_Completed.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Enumerate valid usernames.
- Reduce the complexity of password brute-force attacks.
- Facilitate password spraying attacks.
- Increase the effectiveness of credential stuffing attacks.
- Compromise legitimate user accounts.

---

## Root Cause

The application exposes account existence information by returning different authentication responses for invalid usernames and valid usernames with incorrect passwords.

Authentication responses should never disclose whether a username exists.

Instead, all failed authentication attempts should return identical responses regardless of the failure reason.

---

## Remediation

To prevent this issue:

- Return a generic error message such as:

```
Invalid username or password.
```

for all failed authentication attempts.

Additionally:

- Normalize HTTP status codes.
- Normalize response lengths.
- Normalize response times.
- Implement rate limiting.
- Apply account lockout or progressive delays after repeated failures.
- Deploy Multi-Factor Authentication (MFA) where appropriate.
- Monitor authentication logs for enumeration and brute-force activity.