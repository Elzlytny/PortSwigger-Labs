# Lab 08 - User ID controlled by request parameter, with unpredictable user IDs

## Lab Information

- **Category:** Broken Access Control
- **Difficulty:** APPRENTICE
- **Vulnerability:** User ID controlled by request parameter, with unpredictable user IDs

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

After browsing the application manually, I noticed that each blog post displayed its author's profile.

Visiting these profiles revealed that the application identified users using UUID values in the `userId` query parameter.

After logging in as **wiener**, I observed that the **My Account** page also used the same UUID format.

This suggested that the application relied on a client-controlled identifier to determine which user's account should be displayed.

Based on this observation, I tested whether replacing my own UUID with another user's UUID would bypass the authorization checks.

---

## Discovery and Verification

### Step 1 – Open My Account

Log in as **wiener** and navigate to the **My Account** page.

**Screenshot 1:** Open My Account.

![Step 1](Screenshots/01_Open_My_Account.png)

---

### Step 2 – Modify the User ID Parameter

Intercept the request and replace your UUID with Carlos's UUID obtained from the blog author profile.

Forward the modified request.

The server returns Carlos's account information, including his API key.

**Screenshot 2:** Modify the User ID Parameter.

![Step 2](Screenshots/02_Modify_User_ID_Parameter.png)

---

### Step 3 – Retrieve and Submit Carlos's API Key

Copy Carlos's API key from the account page and submit it to solve the lab.

**Screenshot 3:** Retrieve and Submit Carlos's API Key.

![Step 3](Screenshots/03_Retrieve_And_Submit_Carlos_API_Key.png)

---

## Analysis

The application determines which account to display using a client-controlled `userId` parameter but fails to verify whether the requested account belongs to the authenticated user.

Although unpredictable UUIDs are used instead of sequential identifiers, the application exposes these UUIDs through publicly accessible author profiles.

As a result, an attacker can obtain another user's UUID and use it to access that user's account.

This is a classic example of **Insecure Direct Object Reference (IDOR)** leading to **Horizontal Privilege Escalation**.

---

## Exploitation

An authenticated attacker obtains another user's UUID from a publicly accessible location, such as a blog author profile.

The attacker then intercepts the request to their own account, replaces the `userId` parameter with the target user's UUID, and resends the request.

Because the server does not verify resource ownership, it returns the target user's account information, including sensitive data such as the API key.

---

## Root Cause

The application relies on a client-controlled identifier to determine which account should be returned.

Although UUIDs make identifiers difficult to guess, they are disclosed elsewhere in the application.

The server trusts the supplied identifier without validating that it belongs to the authenticated user.

---

## Impact

Successful exploitation could allow an attacker to:

- Access other users' accounts.
- View sensitive user information.
- Retrieve confidential API keys.
- Compromise the confidentiality of user data.
- Perform horizontal privilege escalation.

---

## Mitigation

To prevent this issue:

- Never trust client-controlled identifiers for authorization decisions.
- Validate ownership of every requested resource on the server side.
- Identify users using the authenticated session whenever possible.
- Do not rely on unpredictable identifiers as an authorization mechanism.
- Implement object-level authorization checks for every request.
- Regularly test applications for IDOR vulnerabilities during security assessments.

---

## Additional Observation

While validating the vulnerability, I performed an additional test by logging out of the application and repeating the request using Carlos's UUID.

Surprisingly, the application still returned Carlos's account information even though no user was authenticated.

This indicates that the endpoint fails to enforce both:

- Authentication
- Object-level authorization

Although unauthenticated users are not provided with a direct navigation link to the endpoint, it remains directly accessible if its URL is known.

As a result, anyone who obtains a valid user UUID and knows the `/my-account` endpoint can access another user's account without logging in.

---

## Key Takeaways

- Unpredictable identifiers are not a substitute for authorization.
- Publicly exposed object identifiers can still be abused.
- Always validate resource ownership on the server side.
- Authentication and authorization must both be enforced.
- Test every user-controlled identifier that references application resources.
- Modifying a single parameter can expose critical authorization vulnerabilities.