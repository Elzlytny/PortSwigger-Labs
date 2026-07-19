# Lab 11 - Insecure direct object references

## Lab Information

- **Category:** Broken Access Control
- **Difficulty:** APPRENTICE
- **Vulnerability:** Insecure Direct Object References (IDOR)

---

## Objective

Obtain Carlos's password and log in to his account.

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

While exploring the application, I discovered a **Live Chat** feature.

After sending a message, the application provided a **View transcript** option that downloaded the chat history as a text file.

The downloaded file was named:

```text
2.txt
```

This suggested that chat transcripts might be stored using predictable file names.

Based on this observation, I tested whether modifying the requested file name would allow access to other users' chat transcripts.

---

## Discovery and Verification

### Step 1 – Open the Live Chat

Open the **Live Chat** feature, send a message, and download the generated transcript.

**Screenshot 1:** Open Live Chat.

![Step 1](Screenshots/01_Open_Live_Chat.png)

---

### Step 2 – Download the Chat Transcript

Intercept the transcript download request.

The application requests:

```text
2.txt
```

**Screenshot 2:** Download Chat Transcript.

![Step 2](Screenshots/02_Download_Chat_Transcript.png)

---

### Step 3 – Modify the Transcript File Name

Replace:

```text
2.txt
```

with:

```text
1.txt
```

The server returns another user's chat transcript containing Carlos's credentials.

**Screenshot 3:** Modify Transcript File Name.

![Step 3](Screenshots/03_Modify_Transcript_File_Name.png)

---

### Step 4 – Log in as Carlos

Use the recovered credentials to authenticate as **Carlos** and complete the lab.

**Screenshot 4:** Login as Carlos and Solve the Lab.

![Step 4](Screenshots/04_Login_As_Carlos_And_Solve_Lab.png)

---

## Analysis

The application exposes chat transcripts through predictable file names without verifying whether the requested file belongs to the current user.

By modifying the file name, an attacker can retrieve another user's transcript containing sensitive information, including login credentials.

This is a classic **Insecure Direct Object Reference (IDOR)** vulnerability caused by missing object-level authorization.

---

## Exploitation

An attacker downloads their own chat transcript and observes that the transcript is stored as a static text file.

By modifying the requested file name from `2.txt` to `1.txt`, the attacker retrieves another user's transcript and obtains Carlos's credentials.

The recovered credentials are then used to authenticate as Carlos.

---

## Root Cause

The application exposes internal file references directly to clients and relies solely on predictable file names to identify resources.

Instead of validating whether the authenticated user is authorized to access the requested transcript, the server returns any existing file.

---

## Impact

Successful exploitation could allow an attacker to:

- Access other users' private chat transcripts.
- Retrieve sensitive information.
- Compromise user accounts.
- Violate the confidentiality of user communications.

---

## Mitigation

To prevent this issue:

- Enforce object-level authorization before serving files.
- Avoid exposing predictable internal file names.
- Store files using unpredictable identifiers.
- Ensure users can only access resources they own.
- Regularly test file download functionality for IDOR vulnerabilities.

---

## Key Takeaways

- File names can be object references and must be protected.
- Predictable resource identifiers significantly increase the risk of IDOR.
- Every file download request should be authorized on the server side.
- Sensitive information should never be exposed through publicly accessible files.