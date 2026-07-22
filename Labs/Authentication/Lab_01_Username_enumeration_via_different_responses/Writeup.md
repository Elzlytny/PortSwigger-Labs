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

During these attempts, I noticed that the application always returned:

> **Invalid username**

This behavior suggested that the application validates whether the username exists before checking the password.

Based on this observation, I hypothesized that supplying a valid username would produce a different server response.

---

## Discovery and Verification

### Step 1 – Open the Login Page

Navigate to the application's login page.

**Screenshot 1:** Login Page.

![Step 1](Screenshots/01_Login_Page.png)

---

### Step 2 – Observe the Default Authentication Response

Submit a login request using a random username and password.

The application responds with:

> Invalid username

**Screenshot 2:** Invalid Username Response.

![Step 2](Screenshots/02_Invalid_Username_Response.png)

---

### Step 3 – Enumerate Valid Usernames

Using Burp Intruder, send login requests with usernames from the provided username list while keeping the password constant.

While reviewing the Intruder results, most responses returned:

- HTTP Status: **200**
- Response Length: **3352**

One response returned:

- HTTP Status: **200**
- Response Length: **3354**

This difference suggested that the supplied username was valid.

**Screenshot 3:** Username Enumeration.

![Step 3](Screenshots/03_Username_Enumeration.png)

---

### Step 4 – Verify the Valid Username

Replay the identified request in Burp Repeater.

Instead of returning:

> Invalid username

the application responds with:

> Incorrect password

This confirms that the username exists.

**Screenshot 4:** Invalid Password Response.

![Step 4](Screenshots/04_Invalid_Password_Response.png)

---

### Step 5 – Brute Force the Password

Now that the valid username has been identified, use Burp Intruder again.

Keep the username fixed and test passwords from the provided password list.

During the attack, one request returned:

- HTTP Status: **302**
- Response Length: **188**

Unlike previous responses, the server also returned:

- A new authenticated session cookie.
- A redirect to the authenticated account.

These indicators confirmed successful authentication.

**Screenshot 5:** Password Brute Force.

![Step 5](Screenshots/05_Password_Brute_Force.png)

---

### Step 6 – Inspect the Successful Authentication Response

Replay the successful request in Burp Repeater.

The server responds with:

- HTTP **302 Found**
- `Set-Cookie`
- `Location: /my-account?id=oracle`

confirming that authentication has succeeded.

**Screenshot 6:** Login Success Request & Response.

![Step 6](Screenshots/06_Login_Success_Request_Response.png)

---

### Step 7 – Verify Authenticated Access

Follow the redirect while preserving the issued session cookie.

The application grants access to the authenticated user's account.

**Screenshot 7:** Authenticated Account.

![Step 7](Screenshots/07_Authenticated_Account.png)

---

### Step 8 – Verify Lab Completion

After successfully authenticating as the victim user, the lab is marked as solved.

**Screenshot 8:** Lab Completed.

![Step 8](Screenshots/08_Lab_Completed.png)

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

After identifying the correct password, the attacker successfully authenticates and gains access to the victim's account.

---

## Root Cause

The application leaks account existence information through inconsistent authentication responses.

Instead of returning a generic authentication failure message for every unsuccessful login attempt, the application reveals whether the supplied username exists before validating the password.

This behavior enables attackers to enumerate valid usernames.

---

## Impact

Successful exploitation could allow an attacker to:

- Enumerate valid user accounts.
- Reduce the complexity of password brute-force attacks.
- Increase the effectiveness of password spraying attacks.
- Improve the success rate of credential stuffing attacks.
- Gain unauthorized access to legitimate user accounts.

---

## Mitigation

To prevent this issue:

- Return the same generic error message for all failed authentication attempts.
- Normalize response bodies.
- Normalize response lengths.
- Normalize response times.
- Implement rate limiting.
- Apply account lockout or progressive delays.
- Deploy Multi-Factor Authentication (MFA) where appropriate.

---

## Key Takeaways

- Authentication responses should never reveal whether a username exists.
- Even small differences in authentication responses can disclose sensitive information.
- Username Enumeration is often the first stage of larger authentication attacks.
- Always compare response messages, response length, status codes, cookies, redirects, and response times during authentication testing.