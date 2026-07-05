# BAC-002 - Unprotected Admin Functionality with Unpredictable URL

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** High

---

## Executive Summary

The application exposes an administrative endpoint without enforcing proper server-side authorization checks. As a result, a regular user can directly access privileged functionality and perform unauthorized administrative actions.

---

## Affected Endpoint

```text
/admin-qt6us5
```

---

## Vulnerability Description

The application fails to enforce authorization checks before granting access to the administrator panel.

Although the administrator interface is not accessible through the application's user interface, the endpoint can be discovered by inspecting the application's source code. Any user who discovers this endpoint can directly access privileged functionality without proper authorization.

This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Discover the Administrator Endpoint

Inspect the application's HTML source code.

The source code reveals the following administrative endpoint:

```text
/admin-qt6us5
```

**Screenshot 1:** Administrator endpoint disclosed in the HTML source code.

![Step 1](Screenshots/01_Source_Code.png)

---

### Step 2 – Access the Administrator Panel

Navigate directly to:

```text
/admin-qt6us5
```

The administrator panel is accessible without authentication or authorization checks.

**Screenshot 2:** Successful access to the administrator panel.

![Step 2](Screenshots/02_Admin_Panel.png)

---

### Step 3 – Perform an Administrative Action

Delete the user **Carlos** from the administrator panel.

**Screenshot 3:** Successful deletion of the user **Carlos**.

![Step 3](Screenshots/03_User_Deleted.png)

---

## Impact

An attacker can:

- Access privileged administrative functionality.
- Perform unauthorized administrative actions.
- Delete user accounts.
- Bypass the application's intended authorization model.

---

## Root Cause

The application relies on using an unpredictable administrator endpoint instead of enforcing proper server-side authorization checks.

Administrative functionality should always verify the user's privileges before processing any request.

---

## Remediation

- Enforce server-side authorization checks on every administrative endpoint.
- Restrict administrative functionality to authorized users only.
- Never rely on hidden, undisclosed, or unpredictable URLs as a security mechanism.
- Apply the Principle of Least Privilege (PoLP).
- Regularly test access control mechanisms during security assessments.