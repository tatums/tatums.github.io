---
layout: post
title: "ssh config"
date: 2015-02-21 07:15:39 -0600
comments: true
categories: ssh linux
---
{% img /images/content/ssh.png %}


I'm in and out of servers all day.  One thing I've found helpful is a ssh config file.  Create a file and place it `~/.ssh/config`.  You can add as many entries as you like.



**Host:** A friendly name/handle which you'll use.

**HostName:** The remote server address you'll be connecting to.

**User:** The user you want to connect as.

**IdentityFile:** The private key to authenticate with.


```
Host s3-rsync
  HostName ec2-54-203-43-190.us-west-2.compute.amazonaws.com
  User ec2-user
  IdentityFile "~/.ssh/bs2-deploy"
```


Once its all setup you can connect by typing: `ssh s3-rsync`

This is the same as: `ssh -i ~/.ssh/bs2-deploy ec2-user@ec2-54-203-43-190.us-west-2.compute.amazonaws.com`.
