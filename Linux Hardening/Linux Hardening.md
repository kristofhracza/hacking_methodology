# System Information
- [ ] Check for files which can be run as root: `sudo -l`
- [ ] Gather OS information
- [ ] Check **$PATH**
	- [ ] Writable folders or files?
- [ ] Check environment variables
- [ ] Running processes: `ps aux`
	- [ ] Is there anything unusual?

# User Information
- [ ] Gather information on all users
	- [ ] ID
	- [ ] Groups
		- [ ] Is the group unusual?
	- [ ] Files
	- [ ] Processes owned / run
- [ ] Clipboards
- [ ] Try to **use** every **known password** that you have discovered previously to login **with each** possible **user**. Try to login also without a password.

# Files and Drives
- [ ] Have you read?
	- [ ] /etc/shadow
	- [ ] /etc/passwd
	- [ ] /etc/hosts
	- [ ] /etc/issue
	- [ ] /home/USERNAME/.bash_history
	- [ ] /home/USERNAME/.profile
- [ ] Have you checked inside?
	- [ ] /tmp
	- [ ] /dev/shm
	- [ ] /opt
	- [ ] /var/log
	- [ ] /usr/sbin
- [ ] Shared files or drives
- [ ] Mounted or unmounted drives
- [ ] Files owned by other users
- [ ] Files modified close to date
- [ ] File location is in an unusual place
- [ ] Binaries in **$PATH**

# Network
- [ ] Enumerate the network: `netstat -tulnp` and `ifconfig`
- [ ] Read: `/proc/net/fib_trie`
- [ ] Ping sweep: `for i in {1..254}; do (ping -c 1 10.10.10.${i} | grep "bytes from" | grep -v "Unreachable" &); done;`

# Cron
- [ ] Check all cron files
- [ ] Is any file or process being modiifed at a regular interval?


# Everything Needed For The Checklist
- [[Commands and DIRs]]
- Exploits and Techniques
	- [[Path Hijacking]]
	- [[Shared Library Misconfiguration]]
	- [[USBCreator D-Bus]]