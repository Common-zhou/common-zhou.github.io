---
layout: post
title:  "centos安装Java8"
categories: Linux
tags: Linux centos java
excerpt: centos安装Java8
auth: zhou
---
* content
{:toc}


## centos7 用yum安装java8

1.查看yum源中是否有相关套件yum -y list java*
![img](/assets/605186-20180921174328475-206969043.png)

2.上图中可以看到有两个自己想用的套件，经过试验发现用yum install java-1.8.0-openjdk 时最后 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_6目录下只用一个jre文件（这跟平时有差异）
最后用 yum -y remove java-1.8.0-openjdk 的方式卸载之后重新 yum -y install java-1.8.0-openjdk-devel.x86_64 才是正常
![img](/assets/605186-20180921175029249-930470966.png)

3.修改/etc/profile并且source /etc/profile

```shell
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64
JRE_HOME=JAVA_HOME/jre
PATH=PATH:JAVA_HOME/bin
CLASSPATH=.:JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export JRE_HOME
export PATH
export CLASSPATH
```

![img](/assets/605186-20180921175203524-914200237.png)





