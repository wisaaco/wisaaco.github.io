---
layout: post
title: "Service and script monitor tool: Monit"
categories: 
    - "Monitoring"
    - "Python"
    - "Monit"
    - "Control"
---


Monit is a system monitoring and error recovery tool [url](https://mmonit.com/monit/)

## This is my recipe:

```bash
apt-get install monit
python3 -m pip install python-telegram-bot --upgrade

sudo vi /etc/monit/monitrc
```

```text
set daemon 30 				#segundos

set httpd port 2812
     allow 0.0.0.0/0.0.0.0
     allow admin:monit


```

myscript.py

```python
import os

pid = str(os.getpid())
with open("/tmp/myscript.pid", "w") as f:
    f.write(pid)

#the code 

    
```

myalert.py

```python
import telegram
import sys
import socket

TELEGRAM_TOKEN = "0000"
CHAT_ID = 00000

if __name__ == '__main__':
    bot = telegram.Bot(token=TELEGRAM_TOKEN)
    message = ""
    if len(sys.argv)>1:
        message = " ".join(sys.argv[1:])
    
    bot.send_message(chat_id=CHAT_ID, text="Alert from %s: %s"%(socket.gethostname(),message))
    
```


file mytest on /etc/monit/conf.d
```txt
check process mytest with pidfile /tmp/myscript.pid
   start = "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 test.py  >> /tmp/view.log 2>&1 &'"
   stop = "/usr/bin/killall python3"
   if does not exist for 3 cycles then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Proceso Mytest No Existe'"
   if cpu > 60% for 2 cycles then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Proceso Mytest CPU up 60'"
   if cpu > 95% for 10 cycles then stop 
   if cpu > 95% for 10 cycles then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Proceso Mytest DETENIDO - CPU up 95'"
   if cpu > 98% for 15 cycles then exec /sbin/reboot
   if cpu > 98% for 15 cycles then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Mytest Reiniciando sistema'"  

   
   if inode usage > 90% then stop
   if inode usage > 90% then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Proceso Mytest DETENIDO - INODE up 90'"
   if inode usage > 95% then exec /sbin/reboot
   if inode usage > 95% then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py Mytest Reiniciando sistema'" 

  


```

Run and test localhost:2812, or monit status
```bash
sudo monit -t
sudo /etc/init.d/monit restart
sudo monit
```