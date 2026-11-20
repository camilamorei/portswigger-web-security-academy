# Unprotected admin functionality (Apprentice)

Platform: PortSwigger Web Security Academy  
Category: Access Control (Broken Access Control)  
Difficulty: Apprentice

## Summary

In this lab, I found an administrative panel that could be accessed without authentication. The application exposed the panel's path in its `robots.txt` file, and the server did not enforce an authorization check when I requested that path directly.

I accessed the administrative functionality and deleted the user `carlos`, which completed the lab.

## Vulnerability category

Broken Access Control - unauthenticated access to administrative functionality.

The application failed to verify whether the requester was authorized to use the administrative panel. The issue was not about bypassing the login form; it was about requesting a sensitive endpoint directly.

## Lab objective

Access the administrator panel and delete the user `carlos`.

## Step-by-step

1. I opened the lab and inspected the login page. I recognized that the objective was not to authenticate through the login form.

   [ADICIONAR PRINT AQUI]

2. I considered that websites sometimes disclose paths through the `robots.txt` file, so I requested `/robots.txt` on the lab's host.

   [ADICIONAR PRINT AQUI]

3. In the response, I found the following directive:

   ```text
   Disallow: /administrator-panel
   ```

4. I navigated directly to `/administrator-panel` without logging in. The application displayed the administrative panel and its user list, which showed that access control was missing.

   [ADICIONAR PRINT AQUI]

5. I clicked `Delete` next to the user `carlos`. The lab confirmed that it was solved.

   [ADICIONAR PRINT AQUI]

## Root cause

The application relied on the obscurity of the administrative URL instead of enforcing authorization on the server. Although the path was not linked from the normal interface, it was discoverable through `robots.txt` and remained accessible to unauthenticated users.

A `robots.txt` directive is a crawler instruction, not an access-control mechanism. Hiding or making a URL difficult to guess does not prevent direct requests.

## Real-world impact

If a production application exposes administrative endpoints without server-side authorization checks, an unauthenticated attacker may be able to perform privileged actions.

Similar issues can affect sensitive routes such as:

- `/admin`
- `/api/internal`
- `/export`

Depending on the exposed functionality, the impact may include unauthorized access to sensitive information, modification of application data, account management actions, or other administrative operations.

The actual impact depends on the actions available through the exposed endpoint and the data or systems it can access.

## Mitigation

- Enforce authorization on the server for every sensitive endpoint and administrative action.
- Deny access by default and explicitly grant permissions to authorized roles.
- Check authorization on every request, not only when rendering links or hiding interface elements.
- Do not rely on obscure URLs, `robots.txt`, or client-side restrictions as security controls.
- Add automated tests to verify that unauthenticated and unauthorized users cannot access administrative functionality.

## References

- [PortSwigger Web Security Academy - Access control](https://portswigger.net/web-security/access-control)
- [PortSwigger Web Security Academy - Unprotected admin functionality](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality)
