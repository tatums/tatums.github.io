---
layout: post
title: "Install Elastic Search on Ubuntu"
date: 2012-12-30 09:07:42 -0600
comments: true
categories:
---

Update Package Manager

```bash
sudo apt-get update
```


Install JRE

```bash
sudo apt-get install openjdk-7-jre-headless -y
```

Install Elastic Search

```
wget http://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.20.2.tar.gz -O elasticsearch.tar.gz
tar -xf elasticsearch.tar.gz
rm elasticsearch.tar.gz
sudo mv elasticsearch-* elasticsearch
sudo mv elasticsearch /usr/local/share
```


Install the Service Wrapper

```
curl -L http://github.com/elasticsearch/elasticsearch-servicewrapper/tarball/master | tar -xz
sudo mv *servicewrapper*/service /usr/local/share/elasticsearch/bin/
rm -Rf *servicewrapper*
sudo /usr/local/share/elasticsearch/bin/service/elasticsearch install
sudo ln -s `readlink -f /usr/local/share/elasticsearch/bin/service/elasticsearch` /usr/local/bin/elasticsearch
sudo service elasticsearch start
curl http://localhost:9200

```
