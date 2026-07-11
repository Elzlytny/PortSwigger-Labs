# BAC Playbook

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements involving Broken Access Control vulnerabilities.

---

# 1. Initial Reconnaissance

- Browse the application manually.
- Understand the application's functionality and business logic.
- Identify user roles and available functionality.
- Map the application's attack surface.

---

# 2. Passive Discovery

- Review the HTML source code.
- Inspect JavaScript files.
- Review HTTP response headers.
- Check common discovery files:
  - `robots.txt`
  - `sitemap.xml`
  - `security.txt`
- Fingerprint the technologies used by the application.

---

# 3. HTTP Traffic Analysis

- Intercept traffic using Burp Suite.
- Compare HTTP requests with their corresponding responses.
- Analyze:
  - Cookies
  - Parameters
  - HTTP Headers
  - Authentication mechanisms
  - Authorization behavior
- Look for:
  - Hidden endpoints
  - Sensitive functionality
  - Security-sensitive parameters
  - Client-controlled data
  - Role identifiers
  - User identifiers
  - Administrative requests

---

# 4. Attack Surface Expansion

If no attack surface is identified:

- Content Discovery
- Directory Enumeration (FFUF)
- Hidden Endpoint Discovery
- Parameter Discovery
- API Endpoint Discovery

---

# 5. Authorization Testing

For every privileged endpoint or action, ask the following questions:

### Is the endpoint directly accessible?

- Try accessing privileged URLs directly.
- Test predictable and discovered endpoints.

---

### Does authorization depend on client-controlled data?

Test whether modifying any of the following changes the application's behavior:

- Cookies
- URL Parameters
- Body Parameters
- HTTP Headers
- Hidden Form Fields

---

### Does the application trust sensitive parameters?

- Compare Requests with Responses.
- Look for parameters such as:
  - `roleId`
  - `isAdmin`
  - `accountType`
- Try adding missing parameters.
- Modify existing parameter values.

---

### Does authorization depend on the HTTP method?

Replay the same request using different HTTP methods such as:

- GET
- POST
- PUT
- PATCH
- DELETE

---

### Does authorization depend on the request path?

If the endpoint returns `401` or `403`, test whether the application trusts alternative ways of identifying the requested resource.

Test rewrite-related headers when appropriate:

- `X-Original-URL`
- `X-Rewrite-URL`

Also test for URL-matching discrepancies, such as:

- Different letter casing
- Trailing slash (`/`)
- File extensions (e.g. `.json`, `.css`, `.anything`)
- Alternative path representations
- URL encoding (when applicable)

Examples:

```text
/admin
/ADMIN
/admin/
/admin.anything
```

If different URL representations reach the same functionality but produce different authorization results, the application may be vulnerable to Broken Access Control.

---

### Can another user's resources be accessed?

Test for Horizontal Privilege Escalation by modifying:

- User IDs
- Object IDs
- Account numbers
- UUIDs
- Other resource identifiers

---

### Does the application enforce business rules correctly?

Test whether application workflows can be bypassed by:

- Skipping steps
- Repeating actions
- Changing the order of requests

---

# 6. Exploitation

- Develop a hypothesis.
- Change one request component at a time.
- Verify the vulnerability.
- Assess its impact.
- Collect supporting evidence.
- Confirm successful exploitation.

---

# 7. Documentation

For every completed lab:

- Document the methodology.
- Write a technical writeup.
- Write a professional vulnerability report.
- Capture screenshots for every significant step.
- Record key lessons learned.
- Update the playbook when a new testing technique or attack pattern is discovered.

---

# Continuous Improvement

This playbook is a living document and will continue to evolve as I learn new attack techniques, tools, and methodologies throughout my web application security journey.