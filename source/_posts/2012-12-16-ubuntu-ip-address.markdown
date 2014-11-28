---
layout: post
title: "Ubuntu ip-address"
date: 2012-12-16 09:14:33 -0600
comments: true
categories:
---

```bash
sudo vim /etc/network/interfaces
```

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet static
address 192.168.1.4
netmask 255.255.255.0
gateway 192.168.1.1
```
