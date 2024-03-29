# Linux
## .dockerenv
If there is a `.dockerenv` file, it means that the host is most likely a docker container

## Shell upgrade to tty
```bash
username@host:~# script /dev/null -c bash

# Press Ctrl+Z
username@host:~# ^Z

# Type: stty raw -echo; fg
localname@localmachine$ stty raw -echo; fg

# Type: reset
reset
reset: unknown terminal type unknown

# Type: screen
Terminal type? screen
                                                                         
username@host:~# 
```

# Local IPs
If there are multiple IPs listed in order, and one is missing (has no port forwarded), one should run an `nmap` scan on the given IP

# Sub-folder or file exposed in non-accessible directory
If access is denied to a folder but there's a resource known to be beyond it / inside it, one can try to access the resource.
Either by changing directories or outputting a file.


# PE Binaries
## x86 or x64
Use `hexdump <binary> -C` to see the first few bytes of the binary.
```
ARHCITECTURE:   x86
50 45 00 00 4c 01 04 00  |........PE..L...|

ARHCITECTURE:   x64
50 45 00 00 64 86 03 00  |........PE..d...|
```
[https://www.gdatasoftware.com/blog/pebitnesstrick](https://www.gdatasoftware.com/blog/pebitnesstrick)