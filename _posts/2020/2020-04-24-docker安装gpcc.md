---
layout: post
title:  "docker安装gpcc"
categories: gpcc
tags: gpcc
excerpt: gpcc
auth: zhou
---
* content
{:toc}


# docker安装gpcc步骤

首先下载gpcc包

之后将gpcc包放到/tmp目录下



```shell
[root用户 -master上]
$ unzip /tmp/greenplum-cc-web-6.0.0-rhel7_x86_64.zip
$ chown -R gpadmin:gpadmin /tmp/greenplum-cc-web-6.0.0-rhel7_x86_64

在全部节点上运行以下命令
chmod 777 /usr/local/

[gpadmin用户  -master上]
/tmp/greenplum-cc-web-6.0.0-rhel7_x86_64/gpccinstall-6.0.0

出现这个标志即可  INSTALLATION COMPLETED SUCCESSFULLY


$ vim ~/.bash_profile(添加下面这行)
source /usr/local/greenplum-cc-web-6.0.0/gpcc_path.sh

$ source ~/.bash_profile 		运行

$ gpcc start

$ psql -h 127.0.0.1 postgres
alter role gpmon with password 'gpmon';

网页上即可使用 gpmon gpmon登录
```





