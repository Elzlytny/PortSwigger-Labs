# Web Penetration Testing Methodology

This document describes the workflow I follow when assessing web applications during PortSwigger labs and future penetration testing engagements.

## 1. Initial Reconnaissance

* Browse the application manually.
* Understand the application's functionality.
* Identify user roles and available features.
* Map the attack surface.

---

## 2. Passive Discovery

* Review page source.

* Review HTTP response headers.

* Inspect JavaScript files.

* Check:

  * robots.txt
  * sitemap.xml
  * security.txt

* Fingerprint technologies used by the application.

---

## 3. Traffic Analysis

* Intercept requests using Burp Suite.
* Review HTTP requests and responses.
* Analyze parameters, cookies, headers, and authentication mechanisms.

---

## 4. Attack Surface Expansion

When necessary:

* Content Discovery
* Directory Enumeration (FFUF)
* Hidden Endpoint Discovery
* Parameter Discovery
* API Endpoint Discovery

---

## 5. Vulnerability Assessment

Common areas to test include:

* Broken Access Control
* SQL Injection
* Cross-Site Scripting (XSS)
* Authentication Issues
* Business Logic Vulnerabilities
* SSRF
* XXE
* File Upload Vulnerabilities

---

## 6. Exploitation

* Verify the vulnerability.
* Assess its impact.
* Collect supporting evidence.

---

## 7. Documentation

For every completed lab:

* Document the reproduction steps.
* Write a technical writeup.
* Write a professional vulnerability report.
* Capture screenshots.
* Record lessons learned.

---

## Continuous Improvement

This methodology is a living document and will be updated as new techniques, tools, and workflows are learned throughout my web security journey.
