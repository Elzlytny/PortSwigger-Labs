# Web Penetration Testing Methodology

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements.

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
  - Session management
- Look for:
  - Hidden endpoints
  - Sensitive parameters
  - Client-controlled data
  - Security-related responses
  - Unusual server behavior

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

Test the identified attack surface for common web vulnerabilities, including:

- Broken Access Control
- SQL Injection
- Cross-Site Scripting (XSS)
- Authentication Vulnerabilities
- Business Logic Vulnerabilities
- Server-Side Request Forgery (SSRF)
- XML External Entity Injection (XXE)
- File Upload Vulnerabilities
- Cross-Site Request Forgery (CSRF)
- Insecure Deserialization
- Information Disclosure
- Security Misconfiguration

---

## 6. Exploitation

- Develop a hypothesis.
- Verify the vulnerability.
- Analyze the application's behavior.
- Assess the impact.
- Collect supporting evidence.
- Confirm successful exploitation.

---

## 7. Documentation

For every completed assessment:

- Document the methodology.
- Record the reproduction steps.
- Write a technical writeup.
- Write a professional vulnerability report.
- Capture screenshots for every significant step.
- Record key lessons learned.
- Update the knowledge base with new concepts and attack patterns.

---

## Continuous Improvement

This methodology is a living document and will continue to evolve as I learn new techniques, tools, and methodologies throughout my web application security journey.