# 🚚 Attack Delivery

After crafting the malicious CSRF payload, the attacker needs a way to make the victim execute it.

Typically, the attacker hosts the malicious HTML page on a website they control and then tricks victims into visiting it. This can be achieved through:

* Phishing emails
* Social media messages
* Malicious advertisements
* Forum posts or comments
* Embedded content on popular websites

Once the authenticated victim visits the malicious page, the browser automatically sends the request together with the victim’s session cookies, causing the action to be executed without the victim’s knowledge.

---

# 🌐 Example: External Malicious Website

The attacker hosts a page containing the malicious auto-submitting form:

```html
<html>
  <body>
    <form method="POST" action="https://vulnerable-website.com/email/change">
      <input type="hidden" name="email" value="pwned@evil-user.net">
    </form>

    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

When the victim visits the attacker-controlled page while logged into the vulnerable application, their email address gets changed automatically.

---

# 🔗 Self-Contained GET-Based CSRF

Some applications incorrectly use the `GET` method for state-changing actions.

In such cases, the attacker may not even need an external malicious website. Instead, they can directly send the victim a crafted URL pointing to the vulnerable application.

Example:

```html
<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">
```

When the victim’s browser loads the image:

* A GET request is automatically sent
* Session cookies are included
* The email address gets changed silently

This type of attack is especially dangerous because it can be triggered simply by:

* Opening an email
* Viewing a webpage
* Loading a comment containing the malicious HTML

---

# ⚠️ Why GET-Based CSRF Is Dangerous

Using `GET` requests for sensitive actions violates secure web design principles because:

* GET requests should be safe and non-state-changing
* Browsers automatically load resources such as images and links
* Attackers can exploit normal browser behavior to trigger unintended actions

Sensitive operations should always:

* Use `POST`
* Require CSRF protection
* Validate request origin
* Use SameSite cookies
