# Authentication Playbook

This document describes the workflow I follow when assessing authentication mechanisms during PortSwigger Web Security Academy labs and future Web Application Penetration Testing engagements involving Authentication vulnerabilities.

---

# Username Enumeration

When testing a login functionality, never immediately start password brute forcing.

First, determine whether the application leaks information about valid usernames.

---

## 1. Initial Manual Testing

Before using any automated tools:

- Submit login requests using random usernames and passwords.
- Carefully observe the application's behavior.
- Compare:

  - Response messages
  - HTTP Status Codes
  - Response Length
  - Response Time
  - Redirect behavior
  - Cookies
  - Response headers

Ask yourself:

- Does the application return different error messages?
- Does the response length change?
- Does the status code change?
- Is a redirect triggered?
- Are any cookies issued?
- Does the application behave differently when the username exists?

---

## 2. Develop a Hypothesis

If different usernames produce different responses, the application may be vulnerable to **Username Enumeration**.

Example:

```
Invalid username
```

vs

```
Incorrect password
```

Even subtle differences such as response length, response time, or redirects may indicate that the username exists.

---

## 3. Username Enumeration

If a hypothesis exists:

- Keep the password fixed.
- Use Burp Intruder with a username wordlist.
- Compare every response.

Pay attention to:

- HTTP Status Codes
- Response Length
- Response Body
- Response Time
- Redirects
- Cookies

Any response that differs from the others should be investigated.

---

## 4. Verify the Finding

Never assume a username is valid based only on Intruder results.

Replay the suspicious request using Burp Repeater.

Verify that the application's response is genuinely different.

Example:

```
Invalid username
```

changes to

```
Incorrect password
```

This confirms that the username exists.

---

## 5. Password Brute Force

Only after confirming a valid username:

- Fix the username.
- Use a password wordlist.
- Monitor:

  - HTTP Status Codes
  - Response Length
  - Redirects
  - Session Cookies

Successful authentication is commonly indicated by:

- HTTP 302
- Set-Cookie
- Redirect to an authenticated page
- Different response length

---

## 6. Verify Successful Authentication

Replay the successful request.

Follow the redirect while preserving the issued session cookie.

Confirm that authenticated resources are accessible.

---

# Lessons Learned

- Never begin with password brute forcing.
- Always test for username enumeration first.
- Build a hypothesis before automating attacks.
- Verify Intruder findings using Burp Repeater.
- Compare every aspect of the response, not only the response body.
- Small behavioral differences can disclose sensitive authentication information.

---

# Continuous Improvement

This playbook is a living document and will continue to evolve as I learn new authentication attack techniques, testing methodologies, and real-world scenarios throughout my Web Application Security journey.