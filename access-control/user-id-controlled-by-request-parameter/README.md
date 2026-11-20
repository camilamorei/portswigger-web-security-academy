# User ID controlled by request parameter (Apprentice)

Platform: PortSwigger Web Security Academy  
Category: Access Control (Broken Access Control)  
Difficulty: Apprentice  
Vulnerability: IDOR (Insecure Direct Object Reference)  
Attack type: Horizontal privilege escalation

## Summary

In this lab, I identified an IDOR vulnerability in the account page. The application used a user-controlled query parameter to determine which account information to display.

By changing the `id` parameter from `wiener` to `carlos`, I accessed another user's account information without authorization. The response exposed Carlos's API key, which I submitted to complete the lab.

## Vulnerability category

Broken Access Control - Insecure Direct Object Reference (IDOR).

IDOR occurs when an application uses a user-controlled identifier to access a resource without properly verifying whether the authenticated user is authorized to access it.

This vulnerability can lead to horizontal privilege escalation, where one user accesses another user's resources at the same privilege level.

## Lab objective

Access the account information of another user and retrieve Carlos's API key.

## Step-by-step

1. I logged in using the credentials provided by the lab:

   - Username: `wiener`
   - Password: `peter`

2. I opened the `My account` page and observed that the application loaded account information through the following URL:

   ```text
   /my-account?id=wiener
   ```

   The `id` parameter identified which user's account information the application would retrieve.

3. On my account page, I could see my own API key, confirming that the application returned sensitive account information based on this parameter.

4. I opened Burp Suite and located the request:

   ```http
   GET /my-account?id=wiener
   ```

   I sent the request to Repeater using `Send to Repeater`.

5. In Repeater, I modified the `id` parameter, replacing `wiener` with `carlos`:

   ```http
   GET /my-account?id=carlos
   ```

6. I clicked `Send` and inspected the server response.

   The application returned Carlos's account information, including his API key, without checking whether my authenticated session had permission to access his account.

7. I copied Carlos's API key and submitted it through the lab's `Submit solution` option.

8. The lab was successfully completed.

## Root cause

The application trusted the `id` query parameter supplied by the client to identify the account whose information should be returned.

However, the server did not verify whether the authenticated user was authorized to access the requested account.

The application therefore allowed one user to retrieve another user's private information simply by changing the identifier in the URL.

## Real-world impact

An IDOR vulnerability can expose sensitive information or allow unauthorized actions against another user's resources.

Depending on the application, an attacker may be able to access:

- Personal profiles and private account information.
- API keys and other sensitive credentials.
- Orders, invoices, and transaction records.
- Private documents and uploaded files.
- Account settings or other user-specific resources.

Identifiers do not need to be sequential numbers. Usernames, UUIDs, email addresses, and other references can also be vulnerable when authorization checks are missing.

## Testing methodology

During an authorized security assessment, I can use the following approach:

1. Identify requests that retrieve or modify user-specific resources.
2. Inspect parameters that identify the requested user or resource.
3. Send the request to Burp Suite Repeater.
4. Change the identifier and resend the request using accounts I am authorized to test.
5. Compare the responses to determine whether the application enforces access control.
6. Document the evidence and report the issue according to the applicable testing scope.

## Mitigation

- Enforce authorization checks on the server for every request that accesses a protected resource.
- Verify that the authenticated user has permission to access the requested object.
- Deny access by default.
- Avoid trusting identifiers supplied by the client as proof of authorization.
- Use indirect references where appropriate, while still enforcing server-side authorization.
- Test access controls using multiple accounts with different ownership and permission levels.
- Ensure that sensitive information, such as API keys, is only returned to authorized users.

## References

- [PortSwigger Web Security Academy - Access control](https://portswigger.net/web-security/access-control)
- [PortSwigger Web Security Academy - Insecure direct object references (IDOR)](https://portswigger.net/web-security/access-control/idor)
- [PortSwigger Web Security Academy - User ID controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter)
