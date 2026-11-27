# User Role Can Be Modified in User Profile

## Overview

This PortSwigger Web Security Academy lab demonstrates a Mass Assignment vulnerability that leads to Broken Access Control and Privilege Escalation.

The application allows authenticated users to update their email address. However, the backend also accepts the `roleid` parameter, allowing users to modify their own privileges.

## Lab Objective

Access the administrative panel at `/admin` and delete the user `carlos`.

**Credentials:**
- Username: `wiener`
- Password: `peter`

## Vulnerability

**Mass Assignment**

The application accepts additional JSON properties during profile updates without properly restricting which fields users are allowed to modify.

By including `roleid: 2` in the email update request, a regular user can obtain administrative privileges.

## Exploitation

### 1. Authentication

Log in using the provided credentials and navigate to the account page.

### 2. Intercept the Email Update Request

Use Burp Suite Proxy to intercept the request generated when updating the account email address.

Example request:

```http
POST /my-account/change-email HTTP/2
Content-Type: application/json

{
    "email": "test@example.com"
}
```

### 3. Send the Request to Repeater

Forward the intercepted request to Burp Suite Repeater to modify its JSON body.

### 4. Modify the User Role

Add the `roleid` parameter with the value `2`:

```json
{
    "email": "test@example.com",
    "roleid": 2
}
```

Send the modified request.

The application accepts the additional parameter and changes the user's role to administrator.

### 5. Access the Admin Panel

Navigate to:

```text
/admin
```

The administrative panel is now accessible because the account has administrative privileges.

### 6. Delete the Target User

Locate the user `carlos` and use the delete function to complete the lab.

## Impact

An attacker with a valid account can escalate privileges by modifying a parameter that should only be controlled by administrators.

Potential consequences include:

- Unauthorized administrative access.
- Privilege escalation.
- Unauthorized user deletion.
- Compromise of administrative functionality.

## Remediation

- Implement server-side allowlists for user-editable fields.
- Never trust role or privilege values supplied by clients.
- Restrict role modifications to authorized administrative operations.
- Enforce authorization checks on every privileged endpoint.
- Use explicit data transfer objects (DTOs) to prevent unintended property assignment.

## Tools

- Burp Suite
- Burp Proxy
- Burp Repeater
- Browser

## References

- [PortSwigger - Mass Assignment Vulnerabilities](https://portswigger.net/web-security/mass-assignment)
- [PortSwigger - Access Control](https://portswigger.net/web-security/access-control)

## Conclusion

This lab demonstrates how improper handling of user-controlled parameters can lead to privilege escalation.

The key takeaway is that backend applications must explicitly control which properties users are allowed to modify, rather than accepting arbitrary fields from client requests.

**Lab Status:** Solved
