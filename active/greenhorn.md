# NMAP SCAN
```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 57:d6:92:8a:72:44:84:17:29:eb:5c:c9:63:6a:fe:fd (ECDSA)
|_  256 40:ea:17:b1:b6:c5:3f:42:56:67:4a:3c:ee:75:23:2f (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
3000/tcp open  ppp?
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=a5dbe9d2b0931eae; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=mytBEDZ1c3TpBWxKeNb0GKQ9rfs6MTcyNTEwNzEwODI2OTUxNzI2OQ; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 31 Aug 2024 12:25:08 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>GreenHorn</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvYX
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=28bc7e5462e6f431; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=S0KXT0d9klkbWD1FFnIbYQPeOOE6MTcyNTEwNzExNDQ3MzU5NTYwNg; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Sat, 31 Aug 2024 12:25:14 GMT
|_    Content-Length: 0
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.94SVN%I=7%D=8/31%Time=66D30BA4%P=x86_64-pc-linux-gnu%r
SF:(GenericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x
SF:20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Ba
SF:d\x20Request")%r(GetRequest,2A60,"HTTP/1\.0\x20200\x20OK\r\nCache-Contr
SF:ol:\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nCo
SF:ntent-Type:\x20text/html;\x20charset=utf-8\r\nSet-Cookie:\x20i_like_git
SF:ea=a5dbe9d2b0931eae;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nSet-Coo
SF:kie:\x20_csrf=mytBEDZ1c3TpBWxKeNb0GKQ9rfs6MTcyNTEwNzEwODI2OTUxNzI2OQ;\x
SF:20Path=/;\x20Max-Age=86400;\x20HttpOnly;\x20SameSite=Lax\r\nX-Frame-Opt
SF:ions:\x20SAMEORIGIN\r\nDate:\x20Sat,\x2031\x20Aug\x202024\x2012:25:08\x
SF:20GMT\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en-US\"\x20class=\"the
SF:me-auto\">\n<head>\n\t<meta\x20name=\"viewport\"\x20content=\"width=dev
SF:ice-width,\x20initial-scale=1\">\n\t<title>GreenHorn</title>\n\t<link\x
SF:20rel=\"manifest\"\x20href=\"data:application/json;base64,eyJuYW1lIjoiR
SF:3JlZW5Ib3JuIiwic2hvcnRfbmFtZSI6IkdyZWVuSG9ybiIsInN0YXJ0X3VybCI6Imh0dHA6
SF:Ly9ncmVlbmhvcm4uaHRiOjMwMDAvIiwiaWNvbnMiOlt7InNyYyI6Imh0dHA6Ly9ncmVlbmh
SF:vcm4uaHRiOjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLC
SF:JzaXplcyI6IjUxMng1MTIifSx7InNyYyI6Imh0dHA6Ly9ncmVlbmhvcm4uaHRiOjMwMDAvY
SF:X")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20t
SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x
SF:20Request")%r(HTTPOptions,1A4,"HTTP/1\.0\x20405\x20Method\x20Not\x20All
SF:owed\r\nAllow:\x20HEAD\r\nAllow:\x20HEAD\r\nAllow:\x20GET\r\nCache-Cont
SF:rol:\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nS
SF:et-Cookie:\x20i_like_gitea=28bc7e5462e6f431;\x20Path=/;\x20HttpOnly;\x2
SF:0SameSite=Lax\r\nSet-Cookie:\x20_csrf=S0KXT0d9klkbWD1FFnIbYQPeOOE6MTcyN
SF:TEwNzExNDQ3MzU5NTYwNg;\x20Path=/;\x20Max-Age=86400;\x20HttpOnly;\x20Sam
SF:eSite=Lax\r\nX-Frame-Options:\x20SAMEORIGIN\r\nDate:\x20Sat,\x2031\x20A
SF:ug\x202024\x2012:25:14\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(RTSPR
SF:equest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/
SF:plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Re
SF:quest");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

___
# Ports
## 80 ->  pluck cms
> found an [exploit](https://github.com/Rai2en/CVE-2023-50564_Pluck-v4.7.18_PoC) for the cms v4.7.18, seems like doesn't require authentication
> found credentials on /settings directory, password was sha512 hashed 
> `admin:d5443aef1b64544f3685bf112f6c405218c573c7279a831b1fe9612e3a4d770486743c5580556c0d838b51749de15530f87fb793afdcc689b6b39024d7790163`

`hashcat -m 1700 -a 0 hash.txt /usr/share/wordlists/rockyou.txt`


> password: iloveyou1

> used the exploit, gained shell, iloveyou1 password works on junior user

> used linpeas.sh, found a binary file that the user can edit and it runs as a service, /usr/local/bin/gitea, but junior user doesn't have privileges to stop/restart a process so there's no way to edit the file (rabbithole)

> junior directory has a pdf file, it contains root password , copied 'Using OpenVAS.pdf' as test.pdf for easy download

```shell
lrwxrwxrwx 1 junior junior     9 Jun 11 14:38  .bash_history -> /dev/null
drwx------ 2 junior junior  4096 Jun 20 06:36  .cache
drwx------ 3 junior junior  4096 Aug 31 15:26  .gnupg
-rw------- 1 junior junior    20 Aug 31 15:40  .lesshst
-rw-r----- 1 junior junior 61367 Aug 31 15:41  test.pdf
-rw-r----- 1 root   junior    33 Aug 31 12:18  user.txt
-rw-r----- 1 root   junior 61367 Jun 11 14:39 'Using OpenVAS.pdf'
```

> root password was blurred on the pdf file, use depix.py to unblur the image (had to look at a write up for this part)

```shell
python3 depix.py -p ../test-000.ppm -s images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png

```

password:sidefromsidetheothersidesidefromsidetheotherside

3000 -> Gitea self hosted git service, contains site files