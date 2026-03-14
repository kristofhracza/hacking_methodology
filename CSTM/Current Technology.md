# Root Squashing
Feature in NFS that prevents remote **root** users on client machines from having the same level of permissions (root) on the NFS server.
This is done by mapping the remote users to an unprivileged account such as `nobody` or `nfsnobody` with *UID 65534*

## TLDR
Basically, don't carry system privileges over to NFS and only give the low privileged accounts.


# How run0 and the polkit can be used as a more secure alternative to sudo on Linux platforms.

## run0
Alternative to `sudo` as it does not use SUID binaries. 
It allows users to execute commands with elevated **(root)** privileges by creating a new, isolated `systemd` service, avoiding the security risks associated with `setuid` binaries. 
`run0` uses `polkit` for authentication and changes terminal colours to highlight privileged sessions.

### TLDR
It does not run commands as **root**, but gives it to a `systemd` service, which already has high privileges. It uses `polkit` for authentication

## polkit
Polkit (PolicyKit) manages system-wide privileges allowing unprivileged processes to communicate with those of higher level.
The `polkit` daemon runs as **root** to be able to mediate privileges.

### TLDR
High level *daemon* that manages privileges between processes with varying levels of privileges.

## Answer
`run0` runs commands in an isolated environment, handing the commands to services, using `polkit` for authentication.
It is more secure because it does not use SUID bits to grant privileges. Which would open up a larger attack surface.
The isolated environment does not inherit anything from the user session, hence preventing attackers to manipulate user sessions.

# What is DMARC (Domain-based Message Authentication, Reporting, and Conformance)?

## SPF (Sender Policy Framework)
Email authentication protocol that protects against spoofing and phishing by listing authorised IPs or domains permnitted.
It uses a DNS TXT record to allow receiving mail servers to verify if an incoming email comes from a trusted source

## DKIM (DomainKeys Identified Mail)
It is is an email authentication protocol that adds a digital signature to outgoing emails, allowing receiving servers to verify that the message was truly sent from the domain and was not altered in transit. It uses public-key cryptography to prevent email spoofing and phishing. 

## Answer
It is an email authentication protocol, that helps protect email domains from spoofing, phishing, and other types of abuse.
DMARC builds on two existing protocols, **SPF (Sender Policy Framework)** and **DKIM (DomainKeys Identified Mail)**, to verify the identity of the sender and the integrity of the message.


# What are the security issues with the FTP protocol?
It transmits data in plain-text.
This makes it vulnerable to attacks such as packet sniffing and Man-in-the-Middle (MitM). It would also allow unauthorised access.
Safer alternatives are SFTP or FTPS.


# What is Kerberos?
Kerberos is a ticket-based computer network authentication protocol. The idea behind Kerberos is to authenticate users while preventing passwords from being sent over the internet.
It uses symmetric-key cryptography, which enables mutual authentication (client and server verify each other) and supports single sign-on (SSO) to securely access network services without constantly re-entering passwords.

## How does it work
Kerberos uses symmetric key cryptography and a Key Distribution Centre (KDC) to authenticate and verify user identities. A KDC involves three aspects:

1. A Ticket-Granting Server (TGS) that connects the user with the Service Server (SS)
2. A Kerberos database that stores the password and identification of all verified users 
3. An Authentication Server (AS) that performs the initial authentication

During authentication, Kerberos stores the specific ticket for each session on the end-user's device. Instead of a password, a Kerberos-aware service looks for this ticket.

Kerberos authentication is a multistep process that consists of the following components: 
1. The client who initiates the need for a service request on the user's behalf 
2. The server, which hosts the service that the user needs access to
3. The AS, which performs client authentication. If authentication is successful, the client is issued a Ticket-Granting Ticket (TGT) or user authentication token, which is proof that the client has been authenticated. 
4. The KDC and its three components: the AS, the TGS, and the Kerberos database
5. The TGS application that issues service tickets


# Directory Traversal
Directory traversal is a web security vulnerability allowing attackers to access unauthorised files, such as source code or system credentials, by manipulating file paths with `../` sequences. By tricking a server into traversing outside the root directory, attackers can read, modify, or delete sensitive data.
It can also be used in file uploads by using the sequences in the file name for example.