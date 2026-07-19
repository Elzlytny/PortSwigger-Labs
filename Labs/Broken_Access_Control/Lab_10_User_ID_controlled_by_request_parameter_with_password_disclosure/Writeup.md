# Lab 10 - User ID controlled by request parameter with password disclosure

## Lab Information

- **Category:** Broken Access Control
- **Difficulty:** APPRENTICE
- **Vulnerability:** User ID controlled by request parameter with password disclosure

---

## Objective

Delete the user **Carlos** by exploiting a broken access control vulnerability.

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

After logging in as **wiener**, I observed that the account page was requested using a client-controlled `id` parameter.

```http
GET /my-account?id=wiener
```

While inspecting the response, I noticed that the user's password was returned inside the HTML as the value of a password input field.

Since the requested account was controlled by the `id` parameter, I tested whether changing the identifier to another user would expose that user's password.

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
id=administrator
```

Forward the modified request.

**Screenshot 2:** Modify the User ID to Administrator.

![Step 2](Screenshots/02_Modify_User_ID_To_Administrator.png)

---

### Step 3 – Retrieve the Administrator Password

Inspect the response and recover the administrator's password from the password input field.

**Screenshot 3:** Retrieve Administrator Password.

![Step 3](Screenshots/03_Retrieve_Administrator_Password.png)

---

### Step 4 – Access the Administrator Panel

Sign in using the recovered administrator credentials and access the administrator panel.

**Screenshot 4:** Access Administrator Panel.

![Step 4](Screenshots/04_Access_Administrator_Panel.png)

---

### Step 5 – Delete Carlos

Delete the user **Carlos** to complete the lab.

**Screenshot 5:** Delete Carlos and Solve the Lab.

![Step 5](Screenshots/05_Delete_Carlos_And_Solve_Lab.png)

---

## Analysis

The application identifies user accounts using a client-controlled `id` parameter and returns sensitive account information without validating whether the authenticated user is authorized to access the requested resource.

Because the password is included in the server response, an attacker can retrieve another user's credentials simply by modifying the identifier.

In this lab, the disclosed credentials belong to the administrator, allowing the attacker to perform a vertical privilege escalation.

---

## Exploitation

An attacker intercepts the request to their own account and replaces the value of the `id` parameter with `administrator`.

The server returns the administrator's account page, including the password embedded in the HTML response.

Using the recovered credentials, the attacker authenticates as the administrator and gains access to privileged functionality.

---

## Root Cause

The application relies on a client-controlled identifier to determine which account should be returned.

Instead of validating resource ownership, the server trusts the supplied identifier and exposes sensitive information that should never be returned to the client.

---

## Impact

Successful exploitation could allow an attacker to:

- Retrieve passwords belonging to other users.
- Compromise privileged accounts.
- Gain unauthorized administrative access.
- Perform vertical privilege escalation.
- Fully compromise the application's access control model.

---

## Mitigation

To prevent this issue:

- Never expose passwords or other secrets in server responses.
- Validate ownership of every requested resource on the server side.
- Identify users using the authenticated session instead of client-controlled parameters.
- Store passwords securely using one-way password hashing.
- Implement object-level authorization checks for every request.

---

## Key Takeaways

- Sensitive information should never be returned to the client.
- Never trust client-controlled identifiers for authorization decisions.
- Authentication and authorization must both be enforced.
- A single IDOR vulnerability can lead to complete administrative compromise when combined with sensitive data disclosure.