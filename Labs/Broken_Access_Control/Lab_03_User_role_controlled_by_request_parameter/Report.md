# BAC-003 - User role controlled by request parameter

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** Critical

---

## Executive Summary

The application relies on a user-controlled cookie to determine administrative privileges without enforcing server-side authorization checks. As a result, a regular user can manipulate the cookie value and gain unauthorized access to privileged functionality.
---

## Affected Components

- Authorization mechanism
- Admin functionality (/admin)
- Client-controlled Admin cookie

---

## Vulnerability Description

The application fails to enforce authorization checks before granting access to the administrator panel by modifying a client-controlled cookie..

Although the administrator interface is not accessible through the application's user interface, an attacker can escalate privileges by manipulating the value of a user controlable parameter. 
This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Identify the Administrator Cookie

Review the intercepted HTTP requests.

The application includes the following cookie:

```text
Admin=false
```

**Screenshot 1:** Administrator cookie identified in the HTTP request.

![Step 1](Screenshots/01_Admin_Cookie.png)

---

### Step 2 – Modify the Cookie Value

Change the cookie value to:

```text
Admin=true
```

Resend the request using Burp Repeater.

The application grants access to administrator functionality.

**Screenshot 2:** Modified administrator cookie.

![Step 2](Screenshots/02_Admin_True.png)

---

### Step 3 – Persist the Cookie in the Browser

Modify the cookie value inside the browser's Storage section:

```text
Admin=true
```

Refresh the page.

The administrator functionality becomes available within the application.

**Screenshot 3:** Modified administrator cookie in the browser.

![Step 3](Screenshots/03_Cookie_Manipulation.png)

---

### Step 4 – Access the Administrator Panel

Navigate to:

```text
/admin
```

The administrator panel is now accessible.

**Screenshot 4:** Successful access to the administrator panel.

![Step 4](Screenshots/04_Admin_Panel.png)

---

### Step 5 – Perform an Administrative Action

Delete the user **Carlos**.

**Screenshot 5:** Successful deletion of the user **Carlos**.

![Step 5](Screenshots/05_Delete_Carlos.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Escalate privileges to administrator.
- Access privileged functionality.
- Perform unauthorized administrative actions.
- Modify or delete application data.
- Fully compromise the application's authorization model.
- Bypass the application's authorization model.

---

## Root Cause

The application relies on a client-controlled cookie to determine user privileges.

Authorization decisions should never be based on data that can be modified by the client.

---

## Remediation

- Perform authorization checks on the server for every privileged request.
- Never trust client-controlled data such as cookies, headers, hidden fields, or request parameters for authorization decisions.
- Store user roles securely on the server.
- Apply the Principle of Least Privilege (PoLP).
- Regularly test access control mechanisms during security assessments.