# User role controlled by request parameter (Apprentice)

Platform: PortSwigger Web Security Academy  
Category: Access Control (Broken Access Control)  
Difficulty: Apprentice  
Status: Solved

## Summary

In this lab, I exploited a broken access control vulnerability caused by the application trusting a client-controlled cookie to determine administrative privileges.

After logging in as a regular user, I inspected the `Admin` cookie and found that it was set to `false`. By changing its value to `true`, I gained access to the administrative panel at `/admin` and deleted the user `carlos`.

## Vulnerability category

Broken Access Control - Client-side access control.

The application relied on a cookie supplied by the browser to determine whether the current user had administrative privileges.

Because the cookie was controlled by the client and was not securely validated by the server, a regular user could modify it and gain unauthorized administrative access.

## Lab objective

Access the `/admin` panel as a regular user and delete the user `carlos`.

## Step-by-step

1. I logged in using the credentials provided by the lab:

   - Username: `wiener`
   - Password: `peter`

2. I attempted to access the administrative panel at:

   ```text
   /admin
   ```

   The application denied access.

3. I inspected the cookies sent by the browser and found the following values:

   ```http
   Cookie: Admin=false; session=nJnEqm7paKaA9OTY20AV7ssTqojqo0i0
   ```

   The `Admin` cookie appeared to control whether the user could access administrative functionality.

4. I modified the `Admin` cookie from `false` to `true`, using the browser's Developer Tools or an intercepted request in Burp Suite.

   ```http
   Cookie: Admin=true; session=nJnEqm7paKaA9OTY20AV7ssTqojqo0i0
   ```

5. I requested `/admin` again with the modified cookie.

   The administrative panel loaded successfully, confirming that the application trusted the client-controlled cookie when making authorization decisions.

6. I located the user `carlos` in the administrative panel and clicked the `Delete` option.

   The application sent a request similar to:

   ```http
   GET /admin/delete?username=carlos
   ```

7. The user `carlos` was deleted, successfully completing the lab.

## Root cause

The application made authorization decisions based on the value of the `Admin` cookie.

Since this cookie was supplied by the client, the user could modify it without needing administrative credentials.

The server treated a client-controlled value as proof of authorization instead of checking the authenticated user's actual permissions through a trusted server-side source.

## Vulnerable implementation

An insecure implementation might look like this:

```python
if request.cookies['Admin'] == 'true':
    liberar_admin()
```

This approach is insecure because the application trusts a value that the user can modify.

## Secure implementation

Authorization should be based on the authenticated user's identity and permissions stored or validated server-side.

For example:

```python
usuario = carregar_usuario(session.user_id)

if usuario.role == 'admin':
    liberar_admin()
```

The server must independently verify the user's role before granting access to administrative functionality.

## Real-world impact

If an application trusts client-controlled cookies to determine user permissions, an attacker may be able to escalate privileges without valid authorization.

Potential consequences include:

- Unauthorized access to administrative panels.
- Modification or deletion of other users' accounts.
- Exposure of sensitive application data.
- Unauthorized changes to system configuration.
- Administrative actions performed by regular users.

## Mitigation

- Never trust client-controlled cookies as proof of authorization.
- Enforce access control on the server for every protected endpoint.
- Retrieve user roles and permissions from a trusted server-side source.
- Associate authorization decisions with the authenticated session.
- Validate permissions before executing sensitive operations.
- Deny access by default.
- Use signed tokens when applicable, and validate their signatures and claims correctly.
- Apply appropriate HTTP methods and protections to sensitive operations. State-changing actions should not be performed through GET requests.

## Key takeaway

Client-side data is not a source of truth.

Anything sent by the browser can potentially be modified, including cookies, URL parameters, hidden form fields, and HTTP headers.

Identity, roles, and permissions must be verified and enforced server-side.

## References

- [PortSwigger Web Security Academy - Access control](https://portswigger.net/web-security/access-control)
- [PortSwigger Web Security Academy - User role controlled by request parameter](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter)
