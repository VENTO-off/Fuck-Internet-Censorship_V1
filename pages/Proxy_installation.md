# Proxy installation guide

> **Note!** Linux based operating system must be installed on the server.

## 1. Proxy installation
```shell
apt-get install squid
```

## 2. Proxy configuration
Open configuration file `/etc/squid/squid.conf` and modify the necessary parameters.

### 2.1 Allow all the incoming connections
```
http_access allow all
http_access deny all        <-- add above this rule
```
> **Note!** This rule must be above the rule `http_access deny all`

### 2.2 Configure caching settings
```
cache_dir ufs /var/spool/squid 4096 32 256
```
- `ufs` &ndash; file system (the best choice for squid);
- `/var/spool/squid` &ndash; cache storage directory;
- `4096` &ndash; amount of space allocated for the cache (in MB);
- `32` &ndash; first-level directories amount for the cache;
- `256` &ndash; second-level directories amount for the cache.

### 2.3 Configure proxy port
```
http_port 1337
```

## 3. Blocking necessary domains (optional)

### 3.1 Define a list of blocked domains
Create a new file `/etc/squid/blacklist` and fill it with the domains that will be blocked:
```
r\.mradx\.net
tns\-counter\.ru
ad\.mail.ru
```

### 3.2 Apply blocked domains rule
Open configuration file `/etc/squid/squid.conf` and add the next rules:
```
acl url_filtred src all
acl blacklist url_regex -i "/etc/squid/blacklist"
http_access deny blacklist url_filtred
http_access allow all       <-- add above this rule
```
> **Note!** This rule must be above the rule `http_access allow all`

## 4. Redirecting traffic to another proxy (optional)
Open configuration file `/etc/squid/squid.conf` and add the next rules:
```
cache_peer <parent_proxy_IP> parent <port> 0 no-query default
acl all src 0.0.0.0/0.0.0.0
http_access allow all       <-- add between this rule
never_direct allow all
```
> **Note!** This rule must be between the rule `http_access allow all`

## 5. Creating folder structure for the cache
### 5.1 Stop a proxy server
```shell
/etc/init.d/squid stop
```

### 5.2 Create folder structure for the cache
```shell
squid -z
```

### 5.3. Start a proxy server
```shell
/etc/init.d/squid start
```

## 6. Scheduling proxy restart
### 6.1 Open crontab
```shell
crontab -e
```

### 6.2 Add a new cron task (restart every day at `04:00`)
```
0 4 * * * /etc/init.d/squid restart
```
