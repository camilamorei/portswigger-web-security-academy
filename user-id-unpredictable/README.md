# PortSwigger Web Security Academy - User ID Controlled by Request Parameter, with Unpredictable User IDs

## Lab Information

- Platform: PortSwigger Web Security Academy
- Difficulty: Apprentice
- Vulnerability: Broken Access Control
- Category: Access Control
- Lab Status: Solved

## Objective

This lab demonstrates a horizontal privilege escalation vulnerability in a user's account page.

The application identifies users using unpredictable GUIDs instead of sequential numeric IDs. The objective is to find Carlos's GUID, access his account, and retrieve his API key.

Credentials provided:

```text
Username: wiener
Password: peter
```

## Understanding the Vulnerability

Horizontal privilege escalation occurs when a user can access another user's resources or information at the same privilege level.

In this lab, the account page uses a user identifier in the request URL. Although the identifiers are unpredictable, they can be discovered elsewhere in the application.

Once Carlos's GUID is identified, it can be used to access his account page without proper authorization checks.

## Methodology

### 1. Find Carlos's GUID

Navigate to the blog section of the application.

Find a blog post written by Carlos and click on his username.

Observe the URL. It contains Carlos's user identifier (GUID).

Save this identifier for later use.

### 2. Log in to Your Account

Use the provided credentials:

```text
Username: wiener
Password: peter
```

After logging in, navigate to your account page.

Observe the URL and identify the `id` parameter.

### 3. Modify the User ID

Replace your user ID in the URL with the GUID previously obtained from Carlos's blog profile.

The request should now reference Carlos's account instead of Wiener's.

### 4. Retrieve the API Key

The application returns Carlos's account information.

Locate his API key and submit it as the lab solution.

## Result

<badge color="success">Lab solved</badge>

Carlos's API key:

```text
YbIFN35fbQS5sDqpS2SIAacnrKpfvdmh
```

## Key Takeaways

- Unpredictable user IDs do not prevent access control vulnerabilities.
- User identifiers exposed elsewhere in an application can be reused.
- Horizontal privilege escalation allows access to another user's data at the same privilege level.
- Authorization must be enforced server-side for every request.
- Applications should verify that the authenticated user is authorized to access the requested resource.

## References

- [PortSwigger - Access Control](https://portswigger.net/web-security/access-control)
- [PortSwigger - Lab: User ID Controlled by Request Parameter, with Unpredictable User IDs](https://portswigger.net/academy/labs/launch/9d95a9e677b81f605d40d00001ffbc8bbb0f0ee73773a3a37727fbde0406b779)
