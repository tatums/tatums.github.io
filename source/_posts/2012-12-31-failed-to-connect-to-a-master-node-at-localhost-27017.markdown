---
layout: post
title: "Failed to Connect to a Master Node at Localhost:27017"
date: 2012-12-31 09:05:06 -0600
comments: true
categories:
---


Everytime my server looses power, Mongo has trouble restarting. These commands fixes the problem.

```bash
sudo rm /var/lib/mongodb/mongod.lock
```

```bash
sudo -u mongodb mongod -f /etc/mongodb.conf --repair
sudo service mongodb restart
```
