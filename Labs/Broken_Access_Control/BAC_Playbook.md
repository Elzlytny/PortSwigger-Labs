# BAC Playbook

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements involving Broken Access Control vulnerabilities.

---

## 1. Initial Reconnaissance

- Browse the application manually.
- Understand the application's functionality and business logic.
- Identify user roles and available features.
- Map the application's attack surface.

---

## 2. Passive Discovery

- Review the HTML source code.
- Inspect JavaScript files.
- Review HTTP response headers.
- Check common discovery files:
  - `robots.txt`
  - `sitemap.xml`
  - `security.txt`
- Fingerprint the technologies used by the application.

---

## 3. HTTP Traffic Analysis

- Intercept traffic using Burp Suite.
- Compare HTTP requests with their corresponding responses.
- Analyze:
  - Request parameters
  - Cookies
  - HTTP headers
  - Authentication mechanisms
  - Authorization behavior
- Look for:
  - Hidden endpoints
  - Security-sensitive parameters
  - Client-controlled data
  - Role identifiers
  - Privileged functionality

---

## 4. Attack Surface Expansion

If no attack surface is identified, perform additional discovery:

- Content Discovery
- Directory Enumeration (FFUF)
- Hidden Endpoint Discovery
- Parameter Discovery
- API Endpoint Discovery

---

## 5. Vulnerability Assessment

Test for common Broken Access Control issues, including:

- Vertical Privilege Escalation
- Horizontal Privilege Escalation
- Context-dependent Access Control
- Insecure Direct Object References (IDOR)
- Client-controlled authorization data
- Missing server-side authorization checks

---

## 6. Exploitation

- Develop a hypothesis.
- Verify the vulnerability.
- Assess the impact.
- Collect supporting evidence.
- Confirm successful exploitation.

---

## 7. Documentation

For every completed lab:

- Document the methodology.
- Write a technical writeup.
- Write a professional vulnerability report.
- Capture screenshots for every significant step.
- Record key lessons learned.
- Update the knowledge base when a new concept or attack pattern is identified.

---

## Continuous Improvement

This methodology is a living document and will continue to evolve as I learn new attack techniques, tools, and methodologies throughout my web application security journey.