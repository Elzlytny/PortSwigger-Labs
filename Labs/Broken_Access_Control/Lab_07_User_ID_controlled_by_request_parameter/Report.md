# BAC-007 - User ID controlled by request parameter

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Horizontal Privilege Escalation (IDOR)
- **Severity:** High

---

## Executive Summary

The application identifies user accounts using a client-controlled `id` query parameter without verifying whether the requested account belongs to the authenticated user.

By modifying the value of the `id` parameter, an authenticated attacker can access another user's account and retrieve sensitive information, resulting in an **Insecure Direct Object Reference (IDOR)** vulnerability.

---

## Affected Components

- User account functionality (`/my-account`)
- Object-level authorization mechanism
- User account information

---

## Vulnerability Description

The application determines which account to display based on the value of the `id` query parameter supplied by the client.

Because the server does not validate that the requested account belongs to the authenticated user, an attacker can simply replace their own user identifier with another user's identifier.

As a result, the application returns another user's account information, including sensitive data such as the user's API key.

This issue results in a **Horizontal Privilege Escalation (IDOR)** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Open My Account

Log in as **wiener** and navigate to the **My Account** page.

**Screenshot 1:** Open My Account.

![Step 1](Screenshots/01_Open_My_Account.png)

---

### Step 2 – Modify the User ID Parameter

Intercept the request and replace:

```text
id=wiener
```

with:

```text
id=carlos
```

Forward the modified request.

**Screenshot 2:** Modify the User ID Parameter.

![Step 2](Screenshots/02_Modify_User_ID_Parameter.png)

---

### Step 3 – Access Carlos's Account

The modified request is processed successfully, allowing access to Carlos's account.

**Screenshot 3:** Access Carlos's Account.

![Step 3](Screenshots/03_Access_Carlos_Account.png)

---

### Step 4 – Retrieve Carlos's API Key

Locate and copy Carlos's API key from the account page.

**Screenshot 4:** Retrieve Carlos's API Key.

![Step 4](Screenshots/04_Retrieve_Carlos_API_Key.png)

---

### Step 5 – Submit Carlos's API Key

Submit Carlos's API key to complete the lab.

**Screenshot 5:** Submit Carlos's API Key.

![Step 5](Screenshots/05_Submit_Carlos_API_Key.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Access other users' accounts.
- Retrieve sensitive information belonging to other users.
- Obtain confidential API keys.
- Compromise the confidentiality of user data.
- Perform horizontal privilege escalation.

---

## Root Cause

The application relies on a client-controlled identifier to determine which account should be returned.

Instead of validating that the requested resource belongs to the authenticated user, the server directly trusts the value supplied in the `id` parameter.

As a result, modifying the identifier allows unauthorized access to another user's account.

---

## Remediation

To prevent this issue:

- Never trust client-controlled identifiers for authorization decisions.
- Identify users based on the authenticated session instead of request parameters whenever possible.
- Validate ownership of every requested resource on the server side.
- Implement object-level authorization checks for every request.
- Follow the Principle of Least Privilege (PoLP).
- Regularly test applications for IDOR vulnerabilities during security assessments.