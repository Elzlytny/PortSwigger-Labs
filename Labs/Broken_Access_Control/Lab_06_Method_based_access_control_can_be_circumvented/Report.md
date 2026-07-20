# BAC-006 - Method-based access control can be circumvented

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** Critical

---

## Executive Summary

The application applies authorization checks only when requests use the `POST` method.

Because authorization is enforced only for a specific HTTP method, an attacker can change the request method to bypass the access control mechanism and perform unauthorized administrative actions.

---

## Affected Components

- Method-based access control mechanism
- Administrative functionality (`/admin`)

---

## Vulnerability Description

The application enforces authorization only when the request uses the `POST` method.

By changing the request method to:

```text
GET
```

the application processes the request without applying the same authorization checks.

As a result, an attacker can bypass the method-based access control mechanism and perform unauthorized administrative actions.

This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Access the Administrator Panel

Sign in as the administrator and navigate to the administrator panel.

**Screenshot 1:** Administrator panel.

![Step 1](Screenshots/01_Login_As_Administrator.png)

---

### Step 2 – Capture the Upgrade Request

Upgrade the user **wiener** while Burp Suite intercepts the request.

**Screenshot 2:** Upgrade request.

![Step 2](Screenshots/02_Admin_Upgrade_Request.png)

---

### Step 3 – Replay the Request as a Regular User

Replace the administrator session with the regular user's session and resend the request.

The server responds with:

```text
401 Unauthorized
```

**Screenshot 3:** Regular user receives `401 Unauthorized`.

![Step 3](Screenshots/03_Regular_User_Unauthorized.png)

---

### Step 4 – Change the HTTP Method

Instead of modifying the endpoint, cookies, headers, or request parameters, change only the HTTP method from:

```text
POST
```

to:

```text
GET
```

Resend the request.

The server responds with:

```text
302 Found
```

**Screenshot 4:** Changing the HTTP method.

![Step 4](Screenshots/04_Method_Changed_POST_To_GET.png)

---

### Step 5 – Verify the Result

Return to the administrator panel and verify that the user **wiener** has been successfully upgraded.

**Screenshot 5:** User successfully upgraded.

![Step 5](Screenshots/05_User_Upgraded_Successfully.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Gain unauthorized access to administrative functionality.
- Bypass method-based access control restrictions.
- Perform unauthorized administrative actions.
- Escalate privileges.
- Fully compromise the application's authorization model.

---

## Root Cause

The application performs authorization checks only when the request uses the `POST` method.

Instead of enforcing authorization regardless of the HTTP method, the application ties its authorization logic to a specific request method.

As a result, changing the HTTP method allows an attacker to bypass the access control mechanism.

---

## Remediation

To prevent this issue:

- Apply authorization checks consistently across all supported HTTP methods.
- Protect administrative functionality rather than individual request methods.
- Deny unexpected HTTP methods when they are not required.
- Apply the Principle of Least Privilege (PoLP).
- Regularly test access control mechanisms during security assessments.