# BAC-005 - URL-based access control can be circumvented

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** High

---

## Executive Summary

The application trusts the client-supplied `X-Original-URL` header when determining the effective request path.

Because this header is accepted and processed without validation, an attacker can manipulate it to bypass URL-based access control and gain unauthorized access to protected administrative functionality.

---

## Affected Components

- URL-based access control mechanism
- Administrative functionality (`/admin`)

---

## Vulnerability Description

The application trusts the client-supplied `X-Original-URL` header when determining the effective request path.

By supplying this header with the value:

```text
X-Original-URL: /admin
```

the application processes the request as if it were targeting the protected administrative endpoint.

The same technique can be applied to privileged administrative actions, such as deleting users, by supplying:

```text
X-Original-URL: /admin/delete
```

As a result, the application bypasses the URL-based authorization mechanism and allows unauthorized administrative actions.

This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Attempt to Access the Administrator Panel

Navigate to:

```text
/admin
```

The server responds with `403 Forbidden`, indicating that access to the endpoint is restricted.

**Screenshot 1:** Access denied when requesting the administrator endpoint.

![Step 1](Screenshots/01_Admin_Access_Denied.png)

---

### Step 2 – Bypass the URL-Based Access Control

Intercept the request and add the following HTTP header:

```text
X-Original-URL: /admin
```

Resend the request using Burp Repeater.

The server accepts and processes the client-supplied header without validation.

**Screenshot 2:** Adding the `X-Original-URL` header.

![Step 2](Screenshots/02_X-Original-URL_Bypass.png)

---

### Step 3 – Access the Administrator Panel

Navigate to:

```text
/admin
```

The administrator panel is now accessible.

**Screenshot 3:** Successful access to the administrator panel.

![Step 3](Screenshots/03_Admin_Panel_Accessed.png)

---

### Step 4 – Attempt to Delete Carlos

Navigate to:

```text
/admin/delete?username=carlos
```

The server again responds with `403 Forbidden`.

**Screenshot 4:** Access denied when attempting to delete the user.

![Step 4](Screenshots/04_Delete_Carlos_Access_Denied.png)

---

### Step 5 – Bypass the Access Control

Intercept the request and add the following HTTP header:

```text
X-Original-URL: /admin/delete
```

Resend the request using Burp Repeater.

The server processes the request as an administrative action and deletes the user.

**Screenshot 5:** Bypassing access control using the `X-Original-URL` header.

![Step 5](Screenshots/05_X-Original-URL_Delete_Bypass.png)

---

### Step 6 – Verify the Result

The user **Carlos** has been successfully deleted, confirming that the access control has been bypassed.

**Screenshot 6:** Successful deletion of the user **Carlos**.

![Step 6](Screenshots/06_Delete_Carlos.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Gain unauthorized access to administrative functionality.
- Bypass URL-based access control restrictions.
- Perform unauthorized administrative actions.
- Modify or delete application data.
- Fully compromise the application's authorization model.

---

## Root Cause

The application trusts the client-supplied `X-Original-URL` header when determining the effective request path.

Instead of performing authorization checks against the original client request, the application processes the rewritten URL without verifying whether the client is authorized to access the requested resource.

As a result, authorization decisions are made based on a user-controlled value.

---

## Remediation

To prevent this issue:

- Do not trust client-supplied rewrite headers such as `X-Original-URL` when making authorization decisions.
- Ignore or remove rewrite headers received directly from untrusted clients.
- Ensure rewrite headers can only be added by trusted reverse proxies.
- Perform authorization checks after URL rewriting has been completed.
- Never trust client-controlled data such as cookies, headers, hidden fields, or request parameters for authorization decisions.
- Apply the Principle of Least Privilege (PoLP).
- Regularly test access control mechanisms during security assessments.

---