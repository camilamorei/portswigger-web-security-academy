# Unprotected admin functionality with unpredictable URL (Apprentice)

Platform: PortSwigger Web Security Academy  
Category: Access Control (Broken Access Control)  
Difficulty: Apprentice

## Summary

In this lab, I found an administrative panel whose URL was unpredictable and was not disclosed in `robots.txt`. Instead of trying to guess the path or log in, I inspected the home page source code.

A JavaScript snippet contained a hidden link to the administrative panel. I extracted the path, accessed it directly without authentication, and deleted the user `carlos`, which solved the lab.

## Vulnerability category

Broken Access Control - unauthenticated access to administrative functionality.

The application hid the administrative link in client-side JavaScript but did not enforce authorization on the server. Hiding a link does not prevent a user from requesting the underlying endpoint directly.

## Lab objective

Discover the unpredictable administrative URL, access the panel, and delete the user `carlos`.

## Step-by-step

1. I opened the lab and inspected the login page. I understood that the objective was not to authenticate through the login form.

2. The administrative path was unpredictable and was not listed in `robots.txt`, so the approach from the previous lab did not apply.

3. I returned to the home page and opened its source code with `Ctrl+U`.

4. I searched the source for the term `admin` and found a JavaScript snippet that controlled whether the administrative link was displayed:

   ```javascript
   var isAdmin = false;
   if (isAdmin) {
       // ...
   }
   ```

5. In the same JavaScript code, I found the assignment that revealed the hidden path:

   ```javascript
   adminPanelTag.setAttribute('href', '/admin-m5skac');
   ```

   This showed that `m5skac` was part of the URL path. The `href` attribute defines a link destination; it was not a password or login credential.

6. I had already noticed the value `m5skac` in Burp Suite, but initially misunderstood it and tried to use it as a password in the login form. After inspecting the source code, I understood that it belonged to the administrative URL.

7. I navigated directly to `/admin-m5skac` without authenticating. The administrative panel loaded, confirming that the server did not enforce the required access control.

8. I clicked `Delete` next to the user `carlos`, completing the lab.

## Root cause

The application relied on client-side JavaScript to hide the administrative link. The `isAdmin` variable was set to `false`, so the link was not displayed in the normal page interface.

However, client-side code is visible and can be inspected or modified by users. More importantly, the server did not verify whether the requester was authorized to access the administrative endpoint. The hidden link therefore provided no actual access control.

## Real-world impact

If an application exposes administrative routes in client-side JavaScript and fails to enforce authorization on the server, an unauthenticated user may discover and directly access sensitive functionality.

During an authorized security assessment or bug bounty engagement, reviewing JavaScript can help identify:

- Hidden application routes and administrative paths.
- API endpoints referenced by front-end code.
- Client-side feature flags and authorization-related UI logic.
- Potentially exposed credentials or sensitive configuration values, which should be handled carefully and reported according to the program rules.

Finding a route in JavaScript does not by itself prove a vulnerability. The security issue exists when the server allows an unauthorized user to access or perform protected actions.

## Mitigation

- Enforce authorization on the server for every sensitive endpoint and administrative action.
- Deny access by default and explicitly grant permissions to authorized roles.
- Validate the user's permissions on every request, regardless of whether a link is visible in the interface.
- Treat all client-side code, variables, feature flags, and hidden links as accessible to users.
- Do not rely on unpredictable URLs or obscured routes as security controls.
- Avoid embedding secrets in front-end code. Store sensitive credentials server-side and rotate any secret that has been exposed.
- Add automated tests to verify that unauthenticated and unauthorized users cannot access administrative functionality.

## References

- [PortSwigger Web Security Academy - Access control](https://portswigger.net/web-security/access-control)
- [PortSwigger Web Security Academy - Unprotected admin functionality with unpredictable URL](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url)
