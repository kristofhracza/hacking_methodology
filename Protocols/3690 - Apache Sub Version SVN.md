# Basic Information
**Apache Subversion (SVN)** is a centralised version control system that allows developers to track changes, manage revisions, and collaborate on files within a shared repository. 
Often accessed over HTTP or HTTPS via the Apache web server using the *mod_dav_svn* module, SVN enables users to commit, update, and review file changes with full history and versioning support.


# Banner Grabbing
```bash
nc -vn <IP> 3690
```

# Enumeration
```sh
# Get files on the server
svn co <url>
# List files
svn ls svn://IP
# Commit history
svn log svn://IP
# Download the whole repo
svn checkout svn://IP
```



