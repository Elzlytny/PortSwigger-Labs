# Lab 09 - User ID controlled by request parameter with data leakage in redirect

## Lab Information

- **Category:** Broken Access Control
- **Difficulty:** APPRENTICE
- **Vulnerability:** User ID controlled by request parameter with data leakage in redirect

---

## Objective

Obtain the API key for the user **Carlos** by exploiting a broken access control vulnerability.

---

## Tools Used

- Web Browser
- Burp Suite

---

## Methodology

Before attempting to solve the lab, I followed my standard web application assessment methodology:

1. Browse the application manually.
2. Understand the application's functionality and business logic.
3. Identify user roles and available functionality.
4. Intercept traffic using Burp Suite.
5. Review HTTP requests and their corresponding responses.
6. Analyze cookies, headers, parameters, and authentication mechanisms.
7. Review the HTML source code and JavaScript files.
8. Check common discovery files.
9. Inspect the Burp Suite Sitemap.
10. Look for sensitive information disclosed in server responses.
11. Test whether client-controlled data influences server-side authorization decisions.
12. Compare how the application behaves before and after authentication (when applicable).
13. If no attack surface is identified, perform content discovery using FFUF.
14. Verify the finding and assess its impact.

---

## Reconnaissance

After logging in as **wiener**, I observed that the application retrieves the account page using a client-controlled `id` parameter.

```http
GET /my-account?id=wiener
```

Based on previous findings, I tested whether modifying the identifier would allow access to another user's account.

When I replaced the value with Carlos's identifier, the browser redirected me to the login page.

Instead of assuming the attack had failed, I inspected the HTTP response in Burp Suite to determine whether any sensitive information had been disclosed before the redirect.

---

## Discovery and Verification

### Step 1 – Open My Account

Log in as **wiener** and navigate to the **My Account** page.

**Screenshot 1:** Open My Account.

![Step 1](Screenshots/01_Open_My_Account.png)

---

### Step 2 – Modify the User ID Parameter

Intercept the request and replace:

```text
id=wiener
```

with:

```text
id=carlos
```

The server responds with:

```text
302 Found
```

**Screenshot 2:** Modify the User ID Parameter.

![Step 2](Screenshots/02_Modify_User_ID_Parameter.png)

---

### Step 3 – Inspect the Redirect Response

Although the server responds with a redirect, the response body still contains Carlos's account information, including his API key.

**Screenshot 3:** Data Leakage in Redirect Response.

![Step 3](Screenshots/03_Data_Leakage_In_302_Response.png)

---

### Step 4 – Submit Carlos's API Key

Copy Carlos's API key from the response body and submit it to solve the lab.

**Screenshot 4:** Lab Solved.

![Step 4](Screenshots/04_Lab_Solved.png)

---

## Analysis

The application performs a redirect after processing the request but includes sensitive account information in the response body before the redirect occurs.

Although the browser automatically follows the redirect and hides the response from the user, an attacker intercepting the traffic can still access the leaked data.

This results in an **Insecure Direct Object Reference (IDOR)** vulnerability combined with sensitive data disclosure.

---

## Exploitation

An attacker modifies the value of the `id` parameter to reference another user's account.

The server responds with `302 Found`, but the response body still contains the requested user's account information before the redirect is processed.

By inspecting the raw HTTP response in Burp Suite, the attacker can recover sensitive information such as the user's API key.

---

## Root Cause

The application generates the requested resource before enforcing the redirect.

Instead of validating authorization before generating sensitive content, the server includes confidential data in the response body and only then redirects the client.

As a result, sensitive information can be disclosed even though the browser does not display it.

---

## Impact

Successful exploitation could allow an attacker to:

- Access sensitive information belonging to other users.
- Retrieve confidential API keys.
- Bypass the protection provided by client-side redirects.
- Compromise the confidentiality of user accounts.
- Perform horizontal privilege escalation.

---

## Mitigation

To prevent this issue:

- Perform authorization checks before generating any sensitive content.
- Never include confidential information in redirect responses.
- Return only the appropriate redirect response without exposing protected resources.
- Validate ownership of every requested resource on the server side.
- Regularly test applications for IDOR and information disclosure vulnerabilities.

---

## Key Takeaways

- Never trust browser behavior when testing authorization.
- Always inspect redirect responses in Burp Suite.
- A `302 Found` response does not guarantee that sensitive data has not been disclosed.
- Authorization must be enforced before generating protected content.