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

#other code


pid = str(os.getpid())
with open("/tmp/myscript.pid", "w") as f:
    f.write(pid)

    
```

myalert.py

```python
import sys

#Telgram, Slack or other notifications
with open("/tmp/messages.txt", "a") as f:
    f.write("Sending things %s\n"%str(sys.argv))

    
```


file mytest on /etc/monit/conf.d
```txt
check process mytest with pidfile /tmp/myscript.pid
   start = "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 test.py  >> /tmp/view.log 2>&1 &'"
   stop = "/usr/bin/killall python3"
   if does not exist for 3 cycles then exec "/bin/su - pi -c 'cd /home/pi; /usr/bin/python3 myalert.py no existe'"
   if inode usage > 80% then alert and exec /path/to/slack.rb
   if inode usage > 99% then stop and exec /path/to/slack.rb
   if cpu > 60% for 2 cycles then alert and exec /path/to/slack.rb
   if cpu > 90% for 10 cycles then restart


```

Run and test localhost:2812, or monit status
```bash
sudo monit -t
sudo /etc/init.d/monit restart
sudo monit
```