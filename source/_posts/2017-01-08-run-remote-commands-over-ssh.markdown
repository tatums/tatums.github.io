---
layout: post
title: "run remote commands over SSH!"
date: 2017-01-08 08:19:15 -0600
comments: true
categories: admin ssh devops
---

Sometimes I find myself needing to run a few command on a remote machine. I'll
usually ssh into the box and hack away. But, there is an a alternative. You can
run command over SSH!

In the example below, I wasnted to keep the configuration files in the git
repository, but wanted a quick and dirty way to deploy the changes.

The first half of the script copies files, using scp, from the local machine to
the remove server.

The second half of the script is run in the context of the remote machine.  The files
are moved to the correct locations and nginx and haproxy are restarted.

```bash
#!/bin/bash

PEM=id_rsa.pub
HOST=ec2-54-234-130-49.compute-1.amazonaws.com

scp -i ~/.ssh/$PEM ./surrogate_pop.conf ubuntu@$HOST:/tmp
scp -i ~/.ssh/$PEM ./haproxy.cfg ubuntu@$HOST:/tmp
scp -i ~/.ssh/$PEM ./traffic_cop.lua ubuntu@$HOST:/tmp
scp -i ~/.ssh/$PEM ./allowed_domains.lua ubuntu@$HOST:/tmp

## These are executed on the remote host
ssh -i ~/.ssh/$PEM ubuntu@$HOST 'bash -s' <<EOF
sudo mv /tmp/traffic_cop.lua /usr/share/nginx/traffic_cop.lua
sudo mv /tmp/allowed_domains.lua /usr/share/nginx/allowed_domains.lua
sudo mv /tmp/surrogate_pop.conf /etc/nginx/sites-enabled/surrogate_pop.conf
sudo service nginx restart

sudo mv /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg
sudo service haproxy restart
EOF
```
