---
title: 使用python获取ip的几种方式
date: 2017-07-04 18:22:38
tags: python
author: dameng
---

# 使用python获取ip的几种方式


## 方式一

使用系统调用

```python
import os
f = os.popen('ifconfig eth0 | grep "inet\ addr" | cut -d: -f2 | cut -d" " -f1')
your_ip=f.read()
print(your_ip)

import os
import re

ipv4 = re.search(re.compile(r'(?<=inet )(.*)(?=\/)', re.M), os.popen('ip addr show eth0').read()).groups()[0]
ipv6 = re.search(re.compile(r'(?<=inet6 )(.*)(?=\/)', re.M), os.popen('ip addr show eth0').read()).groups()[0]
print(ipv4)
print(ipv6)
```

## 方式二

使用socket连接dns服务器

```python
import socket
def get_ip_address():
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    s.connect(("114.114.114.114", 80))
    return s.getsockname()[0]
print(get_ip_address())
```


## 方式三

使用socket, 但不连接远端的服务器

```python
import socket
import fcntl
import struct

def get_ip_address(ifname):
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    return socket.inet_ntoa(fcntl.ioctl(
        s.fileno(),
        0x8915,  # SIOCGIFADDR
        struct.pack('256s', ifname[:15].encode('utf8'))
    )[20:24])

ip = get_ip_address('eth0')  
print(ip)
```


