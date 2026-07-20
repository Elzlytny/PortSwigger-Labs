# Web Penetration Testing Methodology

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements.

The goal of this methodology is to provide a structured, repeatable approach that can be applied across different applications and vulnerability categories.

---

# 1. Initial Reconnaissance

- Browse the application manually.
- Understand the application's functionality and business logic.
- Identify user roles and available features.
- Map the application's attack surface.
- Identify security-sensitive functionality such as:
  - Authentication
  - Registration
  - Password Management
  - Account Settings
  - Administrative Features
  - User Management
  - File Uploads
  - APIs
  - Search Functionality

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

Analyze:

- Request Parameters
- Cookies
- HTTP Headers
- Authentication
- Authorization
- Session Management
- Redirect Responses (`301`, `302`, `307`, `308`)

Look for:

- Hidden endpoints
- Sensitive parameters
- Client-controlled data
- Security-related responses
- Sensitive information disclosure
- Administrative functionality
- Unusual server behavior

---

# 4. Attack Surface Expansion

If no attack surface is identified, perform additional discovery:

- Content Discovery
- Directory Enumeration (FFUF)
- Hidden Endpoint Discovery
- Parameter Discovery
- API Endpoint Discovery

---

# 5. Vulnerability Assessment

For each discovered feature, build a hypothesis and test whether the application properly validates user input and enforces security controls.

Common vulnerability categories include:

- Broken Access Control
- Authentication Vulnerabilities
- SQL Injection
- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- Server-Side Request Forgery (SSRF)
- XML External Entity Injection (XXE)
- File Upload Vulnerabilities
- Path Traversal
- OS Command Injection
- Business Logic Vulnerabilities
- Information Disclosure
- Insecure Deserialization
- Security Misconfiguration

When applicable, test:

- URL Parameters
- Body Parameters
- Cookies
- Client-Controlled Headers
- HTTP Methods
- File Names
- Object Identifiers
- Workflow Logic
- Session Handling

---

# 6. Exploitation

- Develop a hypothesis.
- Change one request component at a time.
- Observe the application's behavior.
- Verify the vulnerability.
- Assess its impact.
- Collect supporting evidence.
- Confirm successful exploitation.

---

# 7. Documentation

For every completed assessment:

- Document the testing methodology.
- Record the reproduction steps.
- Write a technical writeup.
- Write a professional vulnerability report.
- Capture supporting screenshots.
- Record key lessons learned.
- Update category-specific playbooks.
- Update the knowledge base with newly discovered concepts and attack patterns.

---

# Continuous Improvement

This methodology is a living document and will continue to evolve as I learn new techniques, tools, and methodologies throughout my Web Application Security journey.

Rather than documenting individual lab solutions, the goal is to build a practical penetration testing methodology that can be applied during real-world security assessments and bug bounty engagements.