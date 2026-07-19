# BAC-012 - Multi-step process with no access control on one step

## Report Information

- **Category:** Broken Access Control
- **Subcategory:** Vertical Privilege Escalation
- **Severity:** Critical

---

## Executive Summary

The application implements administrative role management through a multi-step workflow.

Although the initial upgrade request correctly enforces authorization, the confirmation request does not perform the same authorization checks.

As a result, an attacker can bypass the protected step and directly invoke the unprotected confirmation request to promote their own account to an administrator.

---

## Affected Components

- Administrative role management
- Multi-step authorization workflow
- Role assignment functionality

---

## Vulnerability Description

The application performs privileged actions using multiple HTTP requests.

Authorization is enforced on the first request but omitted from the confirmation request.

Because each request can be executed independently, an attacker can skip the protected step and directly access the unprotected confirmation endpoint.

This issue results in a **Vertical Privilege Escalation** vulnerability.

---

## Proof of Concept (PoC)

### Step 1 – Open the Administrator Panel

Navigate to the **Admin Panel**.

**Screenshot 1:** Open Admin Panel.

![Step 1](Screenshots/01_Open_Admin_Panel.png)

---

### Step 2 – Capture the Upgrade Workflow

Intercept the **Upgrade Request**.

**Screenshot 2:** Upgrade User Request.

![Step 2](Screenshots/02_Upgrade_User_Request.png)

Intercept the **Confirmation Request**.

**Screenshot 3:** Confirmation Request.

![Step 3](Screenshots/03_Confirmation_Request.png)

---

### Step 3 – Replay the Upgrade Request as Wiener

Replay the first request using **wiener's** session.

The server responds with:

```text
401 Unauthorized
```

**Screenshot 4:** Upgrade Request Unauthorized.

![Step 4](Screenshots/04_Upgrade_Request_Unauthorized.png)

---

### Step 4 – Replay the Confirmation Request as Wiener

Replay only the confirmation request using **wiener's** session.

The server processes the request successfully.

**Screenshot 5:** Confirmation Request Success.

![Step 5](Screenshots/05_Confirmation_Request_Success.png)

---

### Step 5 – Verify the Result

Verify that **wiener** has been promoted to Administrator.

**Screenshot 6:** Wiener Becomes Administrator.

![Step 6](Screenshots/06_Wiener_Becomes_Administrator.png)

---

## Impact

Successful exploitation could allow an attacker to:

- Escalate privileges.
- Bypass authorization controls.
- Perform unauthorized administrative actions.
- Compromise the integrity of the authorization model.

---

## Root Cause

The application assumes that authorization performed during the initial request remains valid throughout the workflow.

Instead of validating permissions on every security-sensitive request, the confirmation endpoint executes privileged actions without performing its own authorization checks.

---

## Remediation

To prevent this issue:

- Enforce authorization checks on every step of multi-step workflows.
- Treat each request as an independent security boundary.
- Never rely on previous requests to guarantee authorization.
- Validate user permissions before executing every privileged operation.
- Perform regular security assessments of workflow-based functionality.