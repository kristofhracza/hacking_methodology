# Insecure Direct Object Reference (IDOR)

IDORs are mainly found in web applications and APIs that rely on user-supplied input to access objects directly.

In an IDOR attack, a bad actor would be able to access data that they're not authorised to see, leading to unauthorised data exposure.



# Overview

1. **Detection:** One needs to identify end-points where user input is used to reference or access internal objects. This can be done by fuzzing, or just studying the application's behaviour.

2. **Exploitation:** When an IDOR vulnerability is found, one could edit the object that is being referenced by another value.



# Examples and Common Attacks

## URL Parameter Reference

A user called `testuser` can access their own profile page *(that contains all their private data)* with the following URL:

```
https://remote.com/user/testuser
```

One could change `testuser` to `testuser2` and it will show the profile of `testuser2`.

**Issue:** The application directly takes the URL and fetches user details without verifying user sessions or without verifying if the given user has access to the URL.

## File Access

```
https://remote.com/uploads/file
```

**Issue:** The website takes the `file` parameter from the user input, resulting in the attacker being able to access any files.

## HTTP Parameter Pollution

The request below allows a user to update their profile.

```
POST /updateProfile
Host: remote.com
Content-Type: application/x-www-form-urlencoded

username=testuser&&bio=New Content
```

### Example 1
In a new request, one can try to add another *username* parameter to the request right after the first one, allowing the last *username* parameter to cancel out the first one and forward the request with the new one.

```
POST /updateProfile
Host: remote.com
Content-Type: application/x-www-form-urlencoded

username=testuser&username=testuser2&bio=New Content
```

**Issue:** The site is not able to handle two parameters of the same kind and therefore allows the last one to go through.

### Example 2
One can simply try overwriting the `username` parameter as such:
```
POST /updateProfile
Host: remote.com
Content-Type: application/x-www-form-urlencoded

username=testuser2&bio=New Content
```

**Issue:** The site seemingly has no back-end logic or security implemented to validate sessions.

# References
- [https://book.hacktricks.wiki/en/pentesting-web/idor.html](https://book.hacktricks.wiki/en/pentesting-web/idor.html)
- [https://owasp.org/Top10/A01_2021-Broken_Access_Control/](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
- [https://hadrian.io/blog/insecure-direct-object-reference-idor-a-deep-dive](https://hadrian.io/blog/insecure-direct-object-reference-idor-a-deep-dive)
- [https://bugbase.in/blog/casting-light-on-IDOR](https://bugbase.in/blog/casting-light-on-IDOR)