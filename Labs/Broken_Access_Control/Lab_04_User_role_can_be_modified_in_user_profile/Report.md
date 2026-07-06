# BAC-004 - User role can be modified in user profile

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** High

---

## Executive Summary

The application exposes the user's role in the server response and accepts the same parameter from the client during profile updates.

By trusting the client-supplied value, the application allows an attacker to modify their own privileges and gain unauthorized administrative access.

---

## Affected Components

- Authorization mechanism
- User profile update functionality
- Admin functionality (`/admin`)

---

## Vulnerability Description

The application accepts a client-controlled `roleId` parameter during profile updates and uses it to determine user privileges without performing proper server-side authorization checks.

An attacker can modify the `roleId` value in the request to elevate their privileges and gain unauthorized access to administrator functionality.

This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Identify the RoleId Parameter

Review the intercepted HTTP requests and their corresponding responses.

The application includes the following parameter:

```text
"roleId":1
```

**Screenshot 1:** RoleId parameter identified in the HTTP response.

![Step 1](Screenshots/01_RoleId_Found.png)

---

### Step 2 – Add the Parameter to the Request

Add the following parameter to the request:

```text
"roleId":1
```

Resend the request using Burp Repeater.

The server accepts the unexpected parameter without returning an error.

**Screenshot 2:** Parameter added to the request.

![Step 2](Screenshots/02_Add_RoleId.png)

---

### Step 3 – Modify the Parameter Value

Change the parameter value to:

```text
"roleId":2
```

Resend the request using Burp Repeater.

The server successfully updates the user's role.

**Screenshot 3:** Modified parameter value.

![Step 3](Screenshots/03_Edit_RoleId_In_Request.png)

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

---

## Root Cause

The application accepts a client-supplied `roleId` parameter and uses it to update user privileges without validating whether the current user is authorized to modify this attribute.

---

## Remediation

To prevent this issue:

- Do not allow clients to modify security-sensitive attributes such as user roles.
- Perform server-side authorization checks before processing any privileged operation.
- Never trust client-controlled data such as cookies, headers, hidden fields, or request parameters for authorization decisions.
- Store user roles securely on the server.
- Apply the Principle of Least Privilege (PoLP).
- Regularly test access control mechanisms during security assessments.