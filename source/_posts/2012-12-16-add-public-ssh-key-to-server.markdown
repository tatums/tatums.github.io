---
layout: post
title: "Add public ssh key to server"
date: 2012-12-16 09:11:56 -0600
comments: true
categories: linux ssh
---

```ruby
cat ~/.ssh/id_rsa.pub | ssh user@hostname 'cat >> .ssh/authorized_keys'
```
