# BAC Playbook

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements involving Broken Access Control vulnerabilities.

---

# 1. Initial Reconnaissance

- Browse the application manually.
- Understand the application's functionality and business logic.
- Identify user roles and available functionality.
- Map the application's attack surface.
- Identify sensitive features such as:
  - User Profiles
  - Admin Panels
  - Account Settings
  - Password Management
  - Role Management
  - Live Chat
  - API Endpoints

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
  - Redirect responses (`301`, `302`, `307`, `308`)
- Look for:
  - Hidden endpoints
  - Sensitive functionality
  - Security-sensitive parameters
  - Client-controlled data
  - Role identifiers
  - User identifiers
  - Administrative requests
  - Sensitive data disclosed in responses (even during redirects)

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

---

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

Compare requests and responses.

Look for parameters such as:

- `roleId`
- `isAdmin`
- `accountType`
- `userId`
- `id`
- `username`

Then:

- Try adding missing parameters.
- Modify existing parameter values.
- Replace identifiers with another user's identifier.

---

### Does authorization depend on the HTTP method?

Replay the same request using different HTTP methods:

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

Also test for URL-matching discrepancies:

- Different letter casing
- Trailing slash (`/`)
- File extensions
- Alternative path representations
- URL encoding

Examples:

```text
/admin
/ADMIN
/admin/
/admin.anything
```

---

### Does authorization depend on client-controlled headers?

Test whether security decisions rely on headers that can be supplied or modified by the client.

Examples include:

- `Referer`
- `Origin`
- `Host`
- `X-Original-URL`
- `X-Rewrite-URL`
- `X-Forwarded-Host`
- `X-Forwarded-For`
- `X-HTTP-Method-Override`

---

### Can another user's resources be accessed?

Test for Horizontal Privilege Escalation by modifying:

- User IDs
- Object IDs
- GUIDs / UUIDs
- Account numbers
- File names
- Other resource identifiers

Also verify whether:

- Authentication is required.
- Authorization is properly enforced.
- Predictable identifiers can be enumerated.
- Indirect references expose sensitive resources.

---

### Does the application disclose sensitive information?

Look for sensitive information returned in:

- Redirect responses (`302`)
- Hidden fields
- Password input values
- JSON responses
- Comments
- Downloadable files
- Error messages

---

### Can files or resources be enumerated?

If files are downloaded using predictable names:

- Change filenames.
- Test numeric sequences.
- Test common extensions.
- Look for sensitive documents.
- Check whether authorization is enforced.

---

### Does the application enforce business rules correctly?

Test whether workflows can be bypassed by:

- Skipping steps
- Repeating actions
- Changing request order
- Replaying requests
- Replacing session cookies between workflow steps

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
- Update the playbook whenever a new testing technique, attack pattern, or mindset is discovered.

---

# Continuous Improvement

This playbook is a living document and will continue to evolve as I learn new attack techniques, tools, and methodologies throughout my Web Application Security journey.

Rather than documenting individual lab solutions, the goal is to build a reusable methodology that can be applied during real-world penetration testing and bug bounty engagements.