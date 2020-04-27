---
layout: post
title:  "greenplum运维相关知识"
categories: greenplum
tags: greenplum
excerpt: greenplum
auth: zhou
---
* content
{:toc}
# greenplum运维



## 1.监控集群的状态

### 1.1 gpstate命令

gpstate是了解gp状态的基本命令，无论数据库是否正常运转，都可以使用该命令

```
如果gpstate无法正常工作，建议排查以下内容
	是否执行了 source <gp安装目录>/greenplum_path.sh
	是否正确设置了MASTER_DATA_DIRECTORY和PGPORT
```

```shell
$ gpstate --help

显示详细信息
$ gpstate -s

显示 primary instance 和 mirror instance 的对应关系
$ gpstate -c

显示 standby master details
$ gpstate -f

显示 mirrors实例的状态和配置信息
$ gpstate -m

显示状态综合信息
$ gpstate -Q

显示版本信息
$ gpstate -i
```

### 1.2系统表gp_segment_configuration

```sql
select * FROM gp_segment_configuration;

--列出当前故障离线的节点
select * from gp_segment_configuration where status = 'd';


```





## 2.管理gp集群

## 3.gpcc