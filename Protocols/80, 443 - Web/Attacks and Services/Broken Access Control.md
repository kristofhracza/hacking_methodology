Access control, sometimes called authorisation, is how a web application grants access to content and functions to some users and not others. These checks are performed after authentication, and govern what ‘authorised’ users are allowed to do. A web application’s access control model is closely tied to the content and functions that the site provides. In addition, the users may fall into a number of groups or roles with different abilities or privileges.

Many of these flawed access control schemes are not difficult to discover and exploit. Frequently, all that is required is to craft a request for functions or content that should not be granted.  In addition to viewing unauthorised content, an attacker might be able to change or delete content, perform unauthorised functions, or even take over site administration.


# Vertical Access Controls
Vertical access controls are mechanisms that restrict access to sensitive functionality to specific types of users.

With vertical access controls, different types of users have access to different application functions. For example, an administrator might be able to modify or delete any user's account, while an ordinary user has no access to these actions. Vertical access controls can be more fine-grained implementations of security models designed to enforce business policies such as separation of duties and least privilege.

# Horizontal access controls
Horizontal access controls are mechanisms that restrict access to resources to specific users.

With horizontal access controls, different users have access to a subset of resources of the same type. For example, a banking application will allow a user to view transactions and make payments from their own accounts, but not the accounts of any other user.

# Context-dependent access controls
Context-dependent access controls restrict access to functionality and resources based upon the state of the application or the user's interaction with it.

Context-dependent access controls prevent a user performing actions in the wrong order. For example, a retail website might prevent users from modifying the contents of their shopping cart after they have made payment.

# References
- [https://owasp.org/www-community/Broken_Access_Control](https://owasp.org/www-community/Broken_Access_Control)
- [https://portswigger.net/web-security/access-control](https://portswigger.net/web-security/access-control)