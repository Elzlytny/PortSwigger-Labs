# Broken Access Control

This directory contains a comprehensive guide covering **concepts, attack scenarios, testing methodologies, and penetration testing mindset**, along with the testing playbook, technical writeups, professional vulnerability reports, and supporting screenshots for the **Broken Access Control** labs from PortSwigger Web Security Academy.

---

## About This Category

Broken Access Control occurs when an application fails to properly enforce restrictions on what authenticated users are allowed to do.

As a result, attackers may gain unauthorized access to functionality, other users' resources, sensitive information, or even administrative privileges.

This category documents not only the solution for each lab, but also a comprehensive security guide covering concepts, attack scenarios, testing methodologies, and a practical penetration testing mindset.

In addition, it includes a dedicated testing playbook, technical writeups, professional vulnerability reports, and supporting screenshots developed throughout the learning process.

---

## Learning Objectives

- Understand how access control works.
- Learn the different types of access control.
- Identify common Broken Access Control vulnerabilities.
- Develop a structured methodology for discovering and exploiting access control flaws.
- Build a practical access control testing playbook.
- Improve technical documentation and vulnerability reporting skills.

---

## Lab Progress

| Lab | Difficulty | Vulnerability | Status |
|------|------------|---------------|--------|
| Lab 01 - Unprotected Admin Functionality | Apprentice | Unprotected Admin Functionality | ✅ |
| Lab 02 - Unprotected Admin Functionality with Unpredictable URL | Apprentice | Unprotected Admin Functionality with Unpredictable URL | ✅ |
| Lab 03 - User role controlled by request parameter | Apprentice | User role controlled by request parameter | ✅ |
| Lab 04 - User role can be modified in user profile | Apprentice | User role can be modified in user profile | ✅ |
| Lab 05 - URL-based access control can be circumvented | Practitioner | URL-based access control can be circumvented | ✅ |
| Lab 06 - Method-based access control can be circumvented | Practitioner | Method-based access control can be circumvented | ✅ |
| Lab 07 - User ID controlled by request parameter | Apprentice | User ID controlled by request parameter (IDOR) | ✅ |
| Lab 08 - User ID controlled by request parameter, with unpredictable user IDs | Apprentice | User ID controlled by request parameter with unpredictable user IDs | ✅ |
| Lab 09 - User ID controlled by request parameter with data leakage in redirect | Apprentice | User ID controlled by request parameter with data leakage in redirect | ✅ |
| Lab 10 - User ID controlled by request parameter with password disclosure | Apprentice | User ID controlled by request parameter with password disclosure | ✅ |
| Lab 11 - Insecure direct object references | Apprentice | Insecure Direct Object Reference (IDOR) | ✅ |
| Lab 12 - Multi-step process with no access control on one step | Practitioner | Multi-step process with no access control on one step | ✅ |
| Lab 13 - Referer-based access control | Practitioner | Referer-based access control | ✅ |

**Progress:** **13 / 13 Labs Completed (100%)** 🎉

---

## Skills Practiced

- Web Application Reconnaissance
- Access Control Testing
- Horizontal Privilege Escalation
- Vertical Privilege Escalation
- Insecure Direct Object Reference (IDOR)
- Authentication vs Authorization Analysis
- Client-Controlled Parameter Testing
- Client-Controlled Header Testing
- HTTP Request Manipulation
- Burp Suite
- FFUF
- Source Code Review
- Technical Documentation
- Professional Vulnerability Reporting

---

## Repository Structure

```text
Broken_Access_Control/
├── README.md
├── Broken_Access_Control_Guide.md
├── BAC_Playbook.md
├── Lab_01_Unprotected_Admin_Functionality/
├── Lab_02_Unprotected_Admin_Functionality_with_Unpredictable_URL/
├── Lab_03_User_role_controlled_by_request_parameter/
├── Lab_04_User_role_can_be_modified_in_user_profile/
├── Lab_05_URL_based_access_control_can_be_circumvented/
├── Lab_06_Method-based_access_control_can_be_circumvented/
├── Lab_07_User_ID_controlled_by_request_parameter/
├── Lab_08_User_ID_controlled_by_request_parameter_with_unpredictable_user_IDs/
├── Lab_09_User_ID_controlled_by_request_parameter_with_data_leakage_in_redirect/
├── Lab_10_User_ID_controlled_by_request_parameter_with_password_disclosure/
├── Lab_11_Insecure_direct_object_references/
├── Lab_12_Multi-step_process_with_no_access_control_on_one_step/
└── Lab_13_Referer-based_access_control/
```

---

## Key Takeaways

Throughout this category, I practiced identifying and exploiting various Broken Access Control vulnerabilities, including:

- Unprotected administrative functionality
- URL-based access control bypasses
- Method-based access control bypasses
- Horizontal Privilege Escalation (IDOR)
- Vertical Privilege Escalation
- Client-controlled parameter manipulation
- Client-controlled header manipulation
- Information disclosure through redirects
- Password disclosure vulnerabilities
- Multi-step workflow authorization flaws

In addition to solving the labs, I documented each finding with:

- A comprehensive security guide covering concepts, attack scenarios, and testing methodologies
- A practical Broken Access Control testing playbook
- Professional technical writeups
- Professional penetration testing reports
- Supporting screenshots

The goal of this repository is not only to document lab solutions, but also to build a reusable methodology for identifying Broken Access Control vulnerabilities in real-world applications.

---

> This category is part of my Web Application Security learning journey through PortSwigger Web Security Academy.