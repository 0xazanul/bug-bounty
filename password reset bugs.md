---

## **1. Password Reset Link Not Expiring**

### Scenario 1

1. Go to `https://site.com/users/forgotten_password`.
2. Request a password reset link and receive it via email.
3. Use the reset link multiple times without it expiring.
4. Observe that the link remains valid even after the password has been reset.

**Impact**: Password reset links do not expire after password changes, allowing attackers to reuse them.
[Reference: HackerOne Report 898841](https://hackerone.com/reports/898841)

---

### Scenario 2

1. Go to `https://infogram.com/forgot` and request a password reset link.
2. Do not use the link and leave it in your inbox.
3. Request another reset link after some time.
4. Use the second link to reset the password.
5. Now, try using the original reset link and observe that it is still functional.

**Impact**: Expired links are still usable after requesting a new link, leading to potential security issues.
[Reference: HackerOne Report 283550](https://hackerone.com/reports/283550)

---

### Scenario 3

1. Create an account or use an existing one.
2. Request a password reset but do not use the code sent to your email.
3. Log back into your account and change the password through the settings.
4. Now use the old reset code and observe that it still allows changing the password.

**Impact**: The reset code remains valid even after the password has been manually changed.
[Reference: HackerOne Report 948345](https://hackerone.com/reports/948345)

---

### Scenario 4

1. Request a password reset link and copy the link without opening it.
2. Log into your account and change your email.
3. Now, use the password reset link you copied earlier and successfully reset the password.

**Impact**: An attacker could change the password using an unexpired reset link, even after the user changes their email.
[Reference: HackerOne Report 685007](https://hackerone.com/reports/685007)

---

### Scenario 5

1. Request a password reset at `https://card.starbucks.com.sg/forgetPassword.php`.
2. Intercept the request using Burp Suite and modify the email to the victim's email.
3. Observe that the password reset link gets sent to the victim’s email, and you can change the password.

**Impact**: This vulnerability allows an attacker to reset the victim's password without any interaction from the victim.
[Reference: HackerOne Report 315879](https://hackerone.com/reports/315879)

---

## **2. Password Reset Token Leak via Referer Header**

1. Request a password reset from `https://ucp.nordvpn.com/lost-password`.
2. After receiving the reset link, click on social media links on the password reset page.
3. Intercept the request using Burp Suite and check the `Referer` header.
4. Observe if the password reset token is being leaked in the `Referer` header.

**Impact**: Leaking the reset token via `Referer` header could allow attackers to reset the victim’s password.
[Reference: HackerOne Report 751581](https://hackerone.com/reports/751581)

---

## **3. Password Reset Token Leak via Response**

1. Request a password reset link at `https://app.xprogram.com/account/forgot-password`.
2. Intercept the response and check if the password reset link is returned in the response body.

**Impact**: Exposing the reset link in the response body can allow attackers to capture and misuse it.
[Reference: Medium Article by YasserGersy](https://medium.com/@yassergersy/account-take-over-via-reset-password-f2e9d887bce1)

---

## **4. Password Reset with Manipulating Email Parameter**

### Test Scenarios:

- Use HTTP Parameter Pollution (HPP): `email=victim@xyz.tld&email=hacker@xyz.tld`
- Carbon copy: `email=victim@xyz.tld%0a%0dcc:hacker@xyz.tld`
- Using separators:
    - `email=victim@xyz.tld,hacker@xyz.tld`
    - `email=victim@xyz.tld|hacker@xyz.tld`
- No domain: `email=victim`
- JSON array:
    
    ```json
    json
    Copy code
    {
      "email": ["victim@xyz.tld", "hacker@xyz.tld"]
    }
    
    ```
    

**Impact**: By manipulating the email parameter, an attacker can receive the reset link for the victim’s account.
[Reference: HackerOne Report 1175081](https://hackerone.com/reports/1175081)

---

## **5. Host Header Poisoning in Password Reset**

1. Request a password reset link and intercept the request in Burp Suite.
2. Modify the `Host` header to `attacker.com` or use:
    - `X-Forwarded-Host: attacker.com`
3. Check if the reset link in the email contains `attacker.com` in the URL.

**Impact**: If successful, the password reset link can be redirected to an attacker-controlled domain.
[Reference: HackerOne Report 226659](https://hackerone.com/reports/226659)

---

## **6. No Rate Limiting on Password Reset**

1. Intercept the password reset request.
2. Use Burp Suite’s Intruder to send multiple requests without rate limiting.
3. Check if the application blocks the requests or allows sending them repeatedly.

**Impact**: Lack of rate limiting could lead to brute-force attacks or email spam.
[Reference: HackerOne Report 838572](https://hackerone.com/reports/838572)

---

## **7. User Enumeration via Password Reset Page**

1. Go to the password reset page and input a valid username.
2. Check if the response redirects you to the login page.
3. Input an invalid username and check if an error message reveals that the account does not exist.

**Impact**: User enumeration can be used by attackers to identify valid accounts on the system.
[Reference: HackerOne Report 77067](https://hackerone.com/reports/77067)

---

## **8. HTML Injection in Password Reset Page**

1. Go to the account creation or password reset page.
2. Insert the following HTML Injection payload in the "First Name" parameter:
    
    ```html
    html
    Copy code
    <a href="http://attacker.com"><h1>Please click here to login to your account</h1></a>
    
    ```
    

**Impact**: HTML Injection could allow an attacker to insert malicious content into the page, leading to phishing or other attacks
