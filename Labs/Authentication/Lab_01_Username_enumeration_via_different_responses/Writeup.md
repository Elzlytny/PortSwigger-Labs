# Lab 01 - Username enumeration via different responses

## Lab Information

- **Category:** Authentication
- **Difficulty:** APPRENTICE
- **Vulnerability:** Username Enumeration

---

## Objective

Identify a valid username by observing differences in the application's authentication responses, then brute-force the corresponding password to gain access to the victim's account.

---

## Tools Used

- Web Browser
- Burp Suite
  - Proxy
  - Intruder
  - Repeater

---

## Methodology

Before attempting to solve the lab, I followed my standard web application assessment methodology:

1. Browse the application manually.
2. Understand the application's functionality and authentication flow.
3. Identify available functionality.
4. Intercept traffic using Burp Suite.
5. Review HTTP requests and their corresponding responses.
6. Analyze cookies, parameters, headers, and authentication behavior.
7. Review the HTML source code and JavaScript files.
8. Check common discovery files.
9. Inspect the Burp Suite Sitemap.
10. Look for differences in authentication responses.
11. Compare server responses for valid and invalid login attempts.
12. Develop and verify an authentication hypothesis.
13. Perform controlled brute-force attacks only after confirming observable differences.
14. Verify successful authentication.

---

## Reconnaissance

After opening the login page, I manually tested several login attempts using random usernames and passwords.

During these attempts, I noticed that the application always returned the message:

> **Invalid username**

This suggested that the application first validates whether the username exists before checking the password.

Based on this observation, I hypothesized that valid usernames might produce a different server response.

---

## Discovery and Verification

### Step 1 – Observe the Default Login Response

Submit a login request using a random username and password.

The application returns:

> Invalid username

**Screenshot 1:** Invalid Username Response.

![Step 1](Screenshots/01_Invalid_Username_Response.png)

---

### Step 2 – Enumerate Valid Usernames

Using Burp Intruder, send login requests with usernames from the provided username list while keeping the password constant.

While reviewing the Intruder results, one request produced a different response length.

Normal responses:

- Status Code: **200**
- Length: **3352**

One response:

- Status Code: **200**
- Length: **3354**

**Screenshot 2:** Intruder Username Enumeration.

![Step 2](Screenshots/02_Username_Enumeration.png)

---

### Step 3 – Verify the Valid Username

Replay the request with the discovered username in Burp Repeater.

Instead of returning:

> Invalid username

the application responds with:

> Incorrect password

This confirms that the username exists.

**Screenshot 3:** Valid Username Confirmation.

![Step 3](Screenshots/03_Valid_Username_Confirmation.png)

---

### Step 4 – Brute Force the Password

Now that the valid username has been identified, use Burp Intruder again.

Keep the username fixed and test passwords from the provided password list.

During the attack, one request returned a significantly different response:

- Status Code: **302**
- Length: **188**

Unlike previous responses, this one also included:

- Session Cookie
- Location Header

indicating successful authentication.

**Screenshot 4:** Password Brute Force Result.

![Step 4](Screenshots/04_Password_Brute_Force.png)

---

### Step 5 – Inspect the Successful Authentication Response

Replay the successful request in Burp Repeater.

The server responds with:

- HTTP 302 Found
- Set-Cookie
- Location: `/my-account?id=oracle`

indicating that authentication has succeeded.

**Screenshot 5:** Successful Login Response.

![Step 5](Screenshots/05_Successful_Login_Response.png)

---

### Step 6 – Access the Victim Account

Follow the redirect while including the issued session cookie.

The application grants access to the victim's account.

**Screenshot 6:** Victim Account.

![Step 6](Screenshots/06_Victim_Account.png)

---

### Step 7 – Verify Lab Completion

After successfully authenticating as the victim user, the lab is marked as solved.

**Screenshot 7:** Lab Solved.

![Step 7](Screenshots/07_Lab_Solved.png)

---

## Analysis

The application exposes different authentication responses depending on whether the supplied username exists.

For invalid usernames, the server returns:

> Invalid username

For valid usernames with incorrect passwords, the server returns:

> Incorrect password

Although this difference appears minor, it allows attackers to distinguish valid accounts from invalid ones.

After identifying a valid username, an attacker can significantly reduce the search space and perform password brute-force attacks only against existing accounts.

---

## Exploitation

An attacker first performs username enumeration by comparing authentication responses.

Once a valid username is discovered, the attacker launches a password brute-force attack against that single account.

Eventually, the correct password is identified, allowing the attacker to authenticate successfully and gain access to the victim's account.

---

## Root Cause

The application leaks account existence information through inconsistent authentication responses.

Instead of returning a generic authentication failure message for all unsuccessful login attempts, the application reveals whether the username exists before validating the password.

This behavior enables attackers to enumerate valid usernames.

---

## Impact

Successful exploitation could allow an attacker to:

- Enumerate valid user accounts.
- Reduce the complexity of password brute-force attacks.
- Compromise user accounts.
- Facilitate credential stuffing attacks using leaked passwords.

---

## Mitigation

To prevent this issue:

- Return the same generic error message for all failed authentication attempts.
- Ensure response bodies remain identical regardless of the failure reason.
- Normalize response length and processing time.
- Implement rate limiting.
- Deploy account lockout policies.
- Use MFA where appropriate.

---

## Key Takeaways

- Authentication responses should never reveal whether a username exists.
- Small differences in response messages, response length, or status codes can disclose sensitive information.
- Username Enumeration often becomes the first stage of larger authentication attacks.
- Always compare authentication responses carefully during testing.