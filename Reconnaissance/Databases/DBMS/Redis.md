# Redis
Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker).

Redis is a text based protocol, you can just send the command in a socket and the returned values will be readable. Also remember that Redis can run using ssl/tls.

# Data Types and Retrieve Commands

```
String GET <key>
HASH HGETALL <key>
LIST lrange <key> <start> <end>
SET smembers <key>
SORTED SETS ZRANGEBYSCORE <key> <min> <max>
```


# Enumeration
```
nmap --script redis-info -sV -p 6379 <IP> msf> use auxiliary/scanner/redis/redis_server
```


# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/6379-pentesting-redis.html](https://book.hacktricks.wiki/en/network-services-pentesting/6379-pentesting-redis.html)
- [https://redis.io/docs/](https://redis.io/docs/)
- [https://fossies.org/linux/redis/TLS.md](https://fossies.org/linux/redis/TLS.md)