# Broken Access Control

This directory contains my notes, concepts, writeups, and professional vulnerability reports for the **Broken Access Control** labs from PortSwigger Web Security Academy.

---

## About This Category

Broken Access Control occurs when an application fails to properly enforce restrictions on what authenticated users are allowed to do.

As a result, attackers may gain access to unauthorized functionality, other users' data, or even administrative privileges.

This repository documents not only the solution for each lab, but also the concepts, methodology, technical writeups, and professional vulnerability reports created throughout the learning process.

---

## Learning Objectives

- Understand how access control works.
- Learn the different types of access control.
- Identify common Broken Access Control vulnerabilities.
- Develop a structured methodology for discovering and exploiting access control flaws.
- Improve technical documentation and reporting skills.

---

## Lab Progress

| Lab | Difficulty | Vulnerability | Status |
|------|------------|---------------|--------|
| Lab 01 - Unprotected Admin Functionality | Apprentice | Unprotected Admin Functionality | ✅ |
| Lab 02 - Unprotected Admin Functionality with Unpredictable URL  |  Apprentice |  Unprotected Admin Functionality with Unpredictable URL | ✅ |
| Lab 03 - User role controlled by request parameter | Apprentice | User role controlled by request parameter | ✅ |
| Lab 04 - User role can be modified in user profile | Apprentice | User role can be modified in user profile | ✅ |
| Lab 05 - URL-based access control can be circumvented | Practitioner | URL-based access control can be circumvented | ✅ |
| Lab 06 - Method-based access control can be circumvented | Practitioner | Method-based access control can be circumvented | ✅ |

---

## Skills Practiced

- Reconnaissance
- Content Discovery
- Source Code Review
- Burp Suite
- FFUF
- Access Control Testing
- Technical Documentation
- Vulnerability Reporting

---

## Repository Structure

```text
Broken_Access_Control/
├── README.md
├── Concepts.md
├── BAC_Playbook.md
├── Lab_01_Unprotected_Admin_Functionality/
│   ├── Writeup.md
│   ├── Report.md
│   └── Screenshots/
│       ├── 01-robots.png
│       └── ...
├── Lab_02_Unprotected_Admin_Functionality_with_Unpredictable_URL/
|   ├── Writeup.md
|   ├── Report.md
|   └── Screenshots/
|       ├── 01_Source_Code.png
|       └── ...
├── Lab_03_User_role_controlled_by_request_parameter/
|   ├── Writeup.md
|   ├── Report.md
|   └── Screenshots/
|       ├── 01_Admin_Cookie.png
|       └── ...
├── Lab_04_User_role_can_be_modified_in_user_profile/
|   ├── Writeup.md
|   ├── Report.md
|   └── Screenshots/
|       ├── 01_RoleId_Found.png
|       └── ...
├── Lab_05_URL_based_access_control_can_becircumvented/
|   ├── Writeup.md
|   ├── Report.md
|   └── Screenshots/
|       ├── 01_Admin_Access_Denied.png
|       └── ...
├── Lab_06_Method-based_access_control_can_be_circumvented/
|   ├── Writeup.md
|   ├── Report.md
|   └── Screenshots/
|       ├── 01_Login_As_Administrator.png
|       └── ...
```

---

> This category is part of my Web Application Security learning journey through PortSwigger Web Security Academy.